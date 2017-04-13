<properties 
    pageTitle="Filtrere og forbehandling i programmet indsigt SDK | Microsoft Azure" 
    description="Skriv Telemetri processorer og Telemetri initialisering for SDK for at filtrere eller føje egenskaber til dataene, før telemetri sendes til portalen programmet indsigt." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrere og forbehandling telemetri i programmet indsigt SDK

*Programmet indsigt er i Vis udskrift.*

Du kan skrive og konfigurere plug-ins til programmet indsigt SDK, til at tilpasse, hvordan telemetri registreres og behandles, før det sendes til tjenesten programmet indsigt. 

I øjeblikket er disse funktioner tilgængelige for ASP.NET SDK.

* [Indsamle](app-insights-sampling.md) reducerer lydstyrken for telemetri uden at påvirke statistikken. Det holder sammen relaterede datapunkter, så du kan navigere mellem dem når diagnosticering et problem. I portalen, er de samlede antal ganges for at kompensation for stikprøverne.
* [Filtrering med Telemetri processorer](#filtering) kan du vælge eller redigere telemetri i SDK, før den er sendt til serveren. For eksempel kan du reducere lydstyrken for telemetri ved at udelukke anmodninger fra fjerne. Men filtrering er en mere grundlæggende fremgangsmåde til at reducere trafik end udvalg. Det giver dig bedre kontrol over, hvad der overføres, men du skal være opmærksom på, at det påvirker statistikken - f.eks, hvis du filtrere alle vellykket anmodninger.
* [Telemetri initialisering tilføjer egenskaber](#add-properties) til en hvilken som helst telemetri, der sendes fra din app, herunder telemetri fra de almindelige moduler. For eksempel kan du tilføje beregnede værdier eller version tal, du vil filtrere data i portalen.
* [SDK API'EN](app-insights-api-custom-events-metrics.md) bruges til at sende brugerdefinerede hændelser og målepunkter.


Før du går i gang:

* Installere [programmet indsigt SDK til ASP.NET v2](app-insights-asp-net.md) i din app. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtrering: ITelemetryProcessor

Denne metode giver dig mere direkte kontrol over, hvad er inkluderet eller udeladt fra telemetri strømmen. Du kan bruge det sammen med udvalg, eller separat.

Hvis du vil filtrere telemetri, skal du skrive en telemetri processor og registrere den med SDK. Alle telemetri går igennem din processor, og du kan vælge at slippe den fra strømmen, eller Tilføj egenskaber. Dette omfatter telemetri fra de almindelige moduler som HTTP-anmodning indsamler og afhængighed indsamler og telemetri, du har skrevet dig selv. Du kan for eksempel filtrere telemetri om anmodninger fra fjerne eller vellykket afhængighed opkald. 

> [AZURE.WARNING] Filtrering telemetri sendes fra SDK kan ved hjælp af processorer vride statistikken, du ser i portalen, og gør det svært at følge relaterede elementer.
> 
> I stedet skal du overveje at bruge [udvalg](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Oprette en telemetri processor

1. Bekræft, at programmet indsigt SDK i dit projekt er version 2.0.0 eller nyere. Højreklik på dit projekt i Visual Studio Solution Explorer, og vælg Administrer NuGet pakker. Markér Microsoft.ApplicationInsights.Web NuGet pakke manager.

1. Hvis du vil oprette et filter, implementere ITelemetryProcessor. Dette er et andet udvidelsesmuligheder af punkt som telemetri modul, telemetri initialisering og telemetri kanal. 

    Bemærk, at Telemetri processorer oprette en kæde af behandling. Når du oprette en forekomst af en telemetri processor, kan du sende et link til den næste processor i kæden. Når et datapunkt telemetri er gået til metoden proces, der fungerer og kalder derefter den næste Telemetri Processor i kæden.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Du kan indsætte det i ApplicationInsights.config: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Dette er den samme sektion, hvor du har initialiseret filteret udvalg).

Du kan overføre strengværdier fra .config-fil ved at indsende offentlige navngivne egenskaber for din klasse. 

> [AZURE.WARNING] Tager sig så det svarer til navnet på og en hvilken som helst egenskabsnavne i .config-filen til klasse og egenskaben navnene i koden. Hvis filen .config henviser til en ikke-eksisterende type eller egenskab, mislykkes muligvis uovervåget SDK til at sende en hvilken som helst telemetri.

 
**Alternativt** kan du initialiseret filteret i kode. Indsætte din processor i rækken i en passende initialisering klasse – for eksempel AppStart i Global.asax.cs:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients, der er oprettet, efter dette punkt anvender din processorer.

### <a name="example-filters"></a>Eksempel filtre

#### <a name="synthetic-requests"></a>Korte anmodninger

Filtrere BOT'er og web test. Selvom målepunkter Explorer giver dig mulighed for at filtrere korte kilder, reducerer denne indstilling for trafik ved at filtrere dem på SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Mislykkedes godkendelse

Filtrere anmodninger med svar "401". 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrere hurtigt ekstern afhængighed opkald

Hvis du kun vil diagnosticere opkald, der er langsom, filtrere dem hurtigt. 

> [AZURE.NOTE] Dette vil vride statistik du ser på portalen. Afhængighed diagrammet ser ud som om afhængighed opkald er alle mislykkede forsøg.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticere afhængighed problemer

[Denne blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) beskrives et projekt for at diagnosticere afhængighed problemer ved at sende automatisk almindelige pinger til afhængigheder.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Tilføj egenskaber: ITelemetryInitializer

Brug telemetri initialisering til at definere globale egenskaber, der sendes med alle telemetri; og for at tilsidesætte markerede funktionsmåde for de almindelige telemetri moduler. 

For eksempel indsamler programmet indsigt til webpakke telemetri om HTTP-anmodninger. Som standard markerer det som mislykket enhver anmodning med et svarkode > = 400. Men hvis du vil behandle 400 som et gunstigt udfald, kan du angive telemetri ved initialisering af en, der angiver egenskaben succes.

Hvis du angiver ved initialisering af en telemetri, kaldes hver gang nogen af Track*() metoderne kaldes. Dette omfatter metoder, der kaldes af de almindelige telemetri moduler. Som konferencen angives disse moduler ikke en egenskab, der allerede er angivet af en initialisering. 

**Definere dine initialisering**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Indlæse din initialisering**

I ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Alternativt kan* du kan indsætte initialisering i kode, for eksempel i Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Se mere af dette eksempel.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>JavaScript telemetri initialisering

*JavaScript*

Indsætte en telemetri initialisering umiddelbart efter den initialisering kode, du fik fra portalen: 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Du kan finde en oversigt over ikke-brugerdefinerede egenskaber tilgængelig på telemetryItem [datamodel](app-insights-export-data-model.md#lttelemetrytypegt).

Du kan tilføje lige så mange initialisering, som du ønsker. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor og ITelemetryInitializer

Hvad er forskellen mellem telemetri processorer og telemetri initialisering?

* Der er nogle overlap i hvad du kan gøre med dem: begge kan bruges til at føje egenskaber til telemetri.
* TelemetryInitializers kører altid før TelemetryProcessors.
* TelemetryProcessors gør det muligt at helt Erstat eller Slet et element i telemetri.
* TelemetryProcessors behandle ikke ydeevne tæller telemetri.



## <a name="persistence-channel"></a>Brugerdata kanal 

Hvis din app kører, hvor internetforbindelsen er ikke altid tilgængelige eller langsom, kan du overveje at bruge salgskanalen brugerdata i stedet for salgskanalen standard i hukommelsen. 

Standard i hukommelsen kanalen mister en hvilken som helst telemetri, der ikke er sendt, når appen lukkes. Selvom du kan bruge `Flush()` for at forsøge at sende data resterende i bufferen, det stadig mister data Hvis der er ingen internetforbindelse, eller Hvis app lukkes ned før overførslen er fuldført.

Derimod placerer salgskanalen brugerdata telemetri i en fil, før du sender den til portalen. `Flush()`sikrer, at dataene gemmes i filen. Hvis data ikke sendes, når appen lukkes, forbliver den i filen. Når appen genstarter, sendes dataene for derefter, hvis der er forbindelse til internettet. Data indsamles i filen, så længe det er nødvendigt, indtil en forbindelse er tilgængelig. 

### <a name="to-use-the-persistence-channel"></a>Bruge salgskanalen brugerdata

1. Importere pakken NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Medtage denne kode i din app på en passende initialisering placering:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Brug `telemetryClient.Flush()` før din app lukkes, at sikre, at dataene er enten sendes til portalen eller gemt i filen.

    Bemærk, at Flush() synkron til brugerdata kanalen, men asynkron for andre kanaler.

 
Salgskanalen brugerdata er udviklet til enheder scenarier, hvor antallet af hændelser, der er oprettet med programmet er relativt lille og forbindelsen er ofte upålidelig. Denne kanal skal skrive begivenheder til disk til pålidelig lagerplads først og derefter forsøge at sende den. 

#### <a name="example"></a>Eksempel

Antag, at du vil overvåge ikke-afviklet undtagelser. Du abonnerer på den `UnhandledException` begivenhed. I tilbagekald, kan du medtage et opkald til træk for at sikre dig, at telemetri bevares.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Når appen er lukket, kan du se en fil i `%LocalAppData%\Microsoft\ApplicationInsights\`, som indeholder de komprimerede begivenheder. 
 
Næste gang du starter programmet, skal kanalen vælge denne fil og leverer telemetri til programmet viden, hvis det er muligt.

#### <a name="test-example"></a>Test eksempel

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Koden for bevarelse af kanalen er på [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Reference dokumenter

* [Oversigt over API](app-insights-api-custom-events-metrics.md)

* [ASP.NET-reference](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>SDK kode

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Næste trin


* [Søg begivenheder og logfiler][diagnostic]
* [Udvalg](app-insights-sampling.md)
* [Fejlfinding i forbindelse med][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
