<properties
   pageTitle="Vedligeholde kommunikation med ASP.NET Web API | Microsoft Azure"
   description="Lær at implementere tjenesten kommunikation trinvise ved hjælp af ASP.NET Web API med OWIN selv vært i API'EN pålidelig tjenester."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Introduktion: Service-strukturen Web API-tjenester med OWIN vært for præsentation

Azure Service-strukturen placerer styrken i fingrene, når du overvejer, hvordan du vil dine tjenester til at kommunikere med brugere og med hinanden. Dette selvstudium fokuserer på implementere tjenesten kommunikation ved hjælp af ASP.NET Web API med åbne Web-grænseflade for selv vært .NET (OWIN) i Service-strukturen pålidelig Services API. Vi vil delve dybt til pålidelig Services tilslutningsbar kommunikationen API. Vi også bruger Web API i en trinvis eksempel viser, hvordan du konfigurerer en brugerdefineret kommunikation lytter.


## <a name="introduction-to-web-api-in-service-fabric"></a>Introduktion til Web API i tjenesten struktur

ASP.NET Web API er en populære og effektive ramme opbygning af HTTP APIs oven på .NET Framework. Hvis du ikke allerede fortrolig med en ramme, du se [Introduktion til ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) for at lære mere.

Web API i tjenesten strukturen er den samme ASP.NET Web API du kender og elsker. Forskellen er i, hvordan du *host* et Web API-program. Du kan ikke bruge Microsoft Internet Information Services (IIS). For bedre at forstå forskellen, Lad os bryde nedarvningen i to dele:

 1. Programmet Web API (herunder enheder og modeller)
 2. Host (web-serveren, som regel IIS)

Et Web API-program selve ændrer ikke. Det er ikke anderledes end Web API-programmer, du har skrevet tidligere, og du bør kunne blot Flyt over mest muligt ud af dine programkode. Men hvis du har vært på IIS, hvor du værten programmet kan være en smule anderledes end det, du er vant til at. Før vi frem til afsnittet hosting, Lad os starte med noget mere velkendte: Web API-programmet.


## <a name="create-the-application"></a>Oprette programmet

Starte med at oprette et nyt Service-strukturen program med en enkelt uden status tjeneste i Visual Studio 2015:

![Oprette et nyt Service-strukturen program](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Visual Studio-skabelon til en uden status tjenesten ved hjælp af Web API er tilgængelige for dig. I dette selvstudium skal vi oprette et Web API projekt fra bunden, der resulterer i hvad du ville få, hvis du har valgt denne skabelon.

Vælg et tomt projekt uden status Service at lære, hvordan du opretter et Web API projekt fra bunden, eller du kan starte med skabelonen uden status service Web API og blot følge.  

![Oprette en enkelt uden status tjeneste](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Det første trin er at trække i nogle NuGet-pakker til Web API. Vi vil bruge pakken er Microsoft.AspNet.WebApi.OwinSelfHost. Pakken indeholder alle de nødvendige Web API-pakker og *host* -pakker. Dette er vigtig senere.

![Oprette Web API ved hjælp af NuGet pakke Manager](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Når pakkerne er blevet installeret, kan du begynde opbygge grundlæggende Web API projektstruktur. Hvis du har brugt Web API, ser project strukturen meget bekendt for dig. Start med at føje en `Controllers` directory og en enkelt værdier controller:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Føj en start-klasse på project-rod til at registrere ruten, formatters og andre konfiguration-konfiguration. Dette er også hvor Web API tilsluttes *host*, som kan være igen senere. 

**Startup.CS**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Det var det for del af programmet. På dette tidspunkt har vi konfigurere lige det grundlæggende Web API Projektlayout. Den ikke bør indtil nu se meget forskellige fra Web API-projekter, du har skrevet tidligere eller den grundlæggende Web API-skabelon. Din forretningslogik går i enheder og modeller på sædvanlig vis.

Nu hvad vi gør om vært, så vi kan faktisk køre programmet?

## <a name="service-hosting"></a>Tjenesten vært

I tjenesten strukturen kører din tjeneste i en *proces for tjenesten host*, en eksekverbar fil, der kører din tjenestekode. Når du skriver en tjeneste ved hjælp af pålidelig Services API, kompilerer projektet service lige til en eksekverbar fil, der registrerer din tjenestetype og kører din kode. Dette er tilfældet i de fleste tilfælde, når du skriver en tjeneste på Service-strukturen i .NET. Når du åbner Program.cs i uden status service projektet, skal du se:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Hvis, der ligner mistænkeligt indgangspunkt til et console-program, er det fordi det er.

Yderligere oplysninger om tjenesten host proces og service registrering er ikke medtaget i denne artikel. Men det er vigtigt at kende nu den *din tjenestekode kører i sin egen proces*.

## <a name="self-host-web-api-with-an-owin-host"></a>Selv er vært Web API med en OWIN vært

Da der er hostet din Web API-programkode i sin egen proces, sådan du slutte den op til en webserver? Angiv [OWIN](http://owin.org/). OWIN er blot en kontrakt mellem .NET webprogrammer og -servere. Traditionelt når ASP.NET (op til 5 MVC) bruges, er webprogrammet tæt forbundet til IIS via sektionsgruppen. Web API implementerer dog OWIN, så du kan skrive et webprogram, der er frakoblet fra den webserver, hvor den. På grund af det, kan du bruge en *præsentation hostet* OWIN webserver, som du kan starte i din egen proces. Dette passer perfekt med tjenesten strukturen hosting modellen vi blot beskrevet.

I denne artikel bruger vi Katana som OWIN vært for Web API-programmet. Katana er en open source-OWIN host implementering, der er baseret på [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) og Windows [HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).

> [AZURE.NOTE] Hvis du vil vide mere om Katana skal du gå til [Katana websted](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Få et hurtigt overblik over, hvordan du bruger Katana du selv er vært Web API skal du se [Brug af OWIN til Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Konfigurere Webserveren

Pålidelige Services API indeholder en kommunikation indgangspunkt, hvor du kan Tilslut kommunikation stakke, der giver brugere og -klienter til at oprette forbindelse til tjenesten:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Webserveren (og alle andre kommunikation stak du i fremtiden vil bruge som WebSockets) skal bruge grænsefladen ICommunicationListener til at integrere korrekt med systemet. Årsager til dette bliver mere synlig i de følgende trin.

Først skal oprette en klasse, kaldet OwinCommunicationListener, der implementerer ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

Grænsefladen ICommunicationListener indeholder tre metoder til at administrere en kommunikation lytter til din tjeneste:

 - *OpenAsync*. Begynd at lytte anmodninger om.
 - *CloseAsync*. Lytte til anmodninger, startet anmodninger er færdig, og Luk problemfrit.
 - *Afbryde*. Annullere alt, og stop med det samme.

For at komme i gang skal du tilføje privat klassemedlemmer for ting lytteren skal fungere. Disse initialiseret gennem parametre og bruges senere, når du konfigurerer lytte URL-adressen.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Implementere OpenAsync

Hvis du vil konfigurere Webserveren, du skal bruge to forskellige elementer:

 - *En URL-sti præfikset*. Men dette er valgfrit, er det godt for dig at konfigurere dette nu så du kan hoste flere webtjenester sikkert i programmet.
 - *En port*.

Før du får en port for webserveren, er det vigtigt, at du forstår, at Service-strukturen indeholder en programlag, der fungerer som en bufferen mellem dit program og den underliggende operativsystemer, der køres på. Tjenesten strukturen giver som sådan en metode til at konfigurere *slutpunkter* for dine tjenester. Tjenesten strukturen sikrer, at slutpunkter tilgængelig for dine tjenester til brug. Denne måde, du ikke behøver at konfigurere dem selv i det underliggende OS-miljø. Du kan nemt hoste programmets Service-strukturen i forskellige miljøer uden at foretage ændringer i dit program. (For eksempel kan du hoste det samme program i Azure eller i dit eget datacenter.)

Konfigurere et HTTP-slutpunkt PackageRoot\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Dette trin er vigtigt, da tjenesten host processen kører under begrænset legitimationsoplysninger (Netværkstjeneste i Windows). Det betyder, at din tjeneste ikke har adgang til konfiguration af en HTTP-slutpunkt på sin egen. Ved hjælp af konfigurationen af slutpunktet, kender Service-strukturen til at konfigurere listen stort access kontrolelement (ACL) til URL-adressen, som tjenesten lytter på. Tjenesten strukturen indeholder også et standard sted for at konfigurere slutpunkter.


Tilbage i OwinCommunicationListener.cs, kan du begynde at implementere OpenAsync. Dette er, du vil starte webserveren. Først skal du få oplysninger, slutpunkt og oprette URL-adressen, som tjenesten lytter på. URL-adressen vil være forskellige afhængigt af om lytteren bruges i en uden status tjeneste eller en med høj sikkerhed. For en med høj sikkerhed tjeneste skal lytteren oprette en entydig adresse for hver med høj sikkerhed service replika den lytter på. Adressen, der kan være meget nemmere for uden status tjenester. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Bemærk, at "http://+" bruges her. Dette er at sørge for, at webserveren lytter på alle tilgængelige adresser, herunder localhost, fulde Domænenavn og IP-computer.

OpenAsync implementering er en af de vigtigste årsager hvorfor webserveren (eller en hvilken som helst kommunikation stak) er implementeret som en ICommunicationListener i stedet for blot får den åbne direkte fra `RunAsync()` i tjenesten. Returværdien fra OpenAsync er den adresse, som webserveren lytter på. Når denne adresse returneres i systemet, registrerer det adressen med tjenesten. Tjenesten strukturen indeholder en API, der gør det muligt for kunder og andre tjenester derefter bede om denne adresse ved navn på tjeneste. Det er vigtigt, da den tjeneste-adresse ikke er statisk. Tjenester flyttes rundt i klynge til ressource justering og tilgængelighed formål. Dette er den metode, gør det muligt for klienter mulighed for at løse lytte adressen for en tjeneste.

Med dette for øje OpenAsync starter webserveren og returnerer den adresse, som den lytter på. Bemærk, at det lytter på "http://+", men før OpenAsync returnerer adressen, den "+" erstattes med den IP- eller det fulde Domænenavn for noden det aktuelt er på. Den adresse, der returneres af denne metode er, hvad er registreret i systemet. Det er også det kunder og andre tjenester ser, når de anmoder om en tjeneste adresse. For klienter korrekt oprettes forbindelse til den, de skal bruge en faktisk IP-adresse eller fulde webadresse.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Bemærk, at dette henviser til klassen Start, som blev sendt til OwinCommunicationListener i konstruktøren. Forekomsten Start bruges af webserveren skal initialiseres Web API-programmet.

Den `ServiceEventSource.Current.Message()` linje, vises i vinduet diagnosticering begivenheder senere, når du kører programmet for at bekræfte, at webserveren har startet korrekt.

## <a name="implement-closeasync-and-abort"></a>Implementere CloseAsync og afbrydelse

Til sidst skal implementere både CloseAsync og Afbryd for at stoppe Webserveren. Webserveren, kan den standses ved salg håndtaget server, der blev oprettet under OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

I dette eksempel implementering stoppe både CloseAsync og Afbryd blot webserveren. Du kan vælge for at udføre en mere problemfrit koordineret lukning af webserveren i CloseAsync. Lukningen kan for eksempel Vent startet anmodninger om at være fuldført, før du vender tilbage.

## <a name="start-the-web-server"></a>Starte webserveren

Nu er du klar til at oprette og returnere en forekomst af OwinCommunicationListener til at starte webserveren. Tilbage i klassen Service (WebService.cs), tilsidesætte de `CreateServiceInstanceListeners()` metode:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Dette er hvor Web API- *programmet på computeren* og OWIN *host* til sidst opfylder. Host (OwinCommunicationListener) gives en forekomst af *programmet* (Web API) via klassen Start. Tjenesten strukturen administrerer derefter dets livscyklus. Denne samme mønster kan typisk efterfølges med en hvilken som helst kommunikation stak.

## <a name="put-it-all-together"></a>Placer det alle sammen

I dette eksempel skal du ikke behøver at gøre noget den `RunAsync()` metode, så Tilsidesæt kan blot fjernes.

Den endelige implementering skal være meget simpel. Det skal kun oprette kommunikation lytteren:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

Den komplette `OwinCommunicationListener` klasse:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

Nu hvor du har indsat alle elementerne på plads, kan dit projekt skal se ud som et typisk Web API-program med pålidelig Services API indgangspunkter og en OWIN vært:


![Web API med pålidelig Services API indgangspunkter og OWIN host](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Kør og oprette forbindelse via en webbrowser

Hvis du ikke har gjort det, skal du [konfigurere din udviklingsmiljø](service-fabric-get-started.md).


Du kan nu opbygge og anvende din tjeneste. Tryk på **F5** i Visual Studio til at opbygge og anvende programmet. I vinduet diagnosticering begivenheder, skal du se en meddelelse, der angiver, at webserveren åbnet på http://localhost:8281 /.


![Visual Studio diagnosticering begivenheder vindue](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Hvis port er allerede blevet åbnet af en anden proces på computeren, kan du se fejlen her. Dette angiver, at lytteren ikke kunne åbnes. Hvis det er tilfældet, kan du prøve at bruge en anden port for konfigurationen af slutpunktet i ServiceManifest.xml.


Når tjenesten kører, Åbn en browser, og gå til [api-http://localhost:8281/værdier](http://localhost:8281/api/values) for at teste den.

## <a name="scale-it-out"></a>Skalere ud af det

Skalering af uden status Onlines typisk betyder, at tilføje flere computere og rotering af webapps på dem. Tjenesten strukturen organisering program kan gøre det for dig, når nye noder, der er føjet til en klynge. Når du opretter forekomster af en uden status tjeneste, kan du angive antallet forekomster, du vil oprette. Tjenesten strukturen placerer pågældende antallet af forekomster på noderne i klyngen. Og det sikrer, at ikke at oprette mere end én forekomst på en hvilken som helst en node. Du kan også angive Service-strukturen altid skal oprette en forekomst på hver node ved at angive **-1** for antallet forekomst. Dette garanterer, at når du har tilføjet noder til at skalere ud af din klynge, en forekomst af din uden status tjeneste oprettes på de nye noder. Denne værdi er en egenskab for forekomsten af tjenesten, så det er angivet, når du opretter en forekomst af tjenesten. Du kan gøre dette via PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Du kan også gøre dette, når du definerer en standardtjeneste i et Visual Studio uden status service projekt:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Du kan finde flere oplysninger om hvordan du opretter programmet og forekomster af tjenesten, [Implementer et program](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Næste trin

[Fejlfinding programmets Service-strukturen ved hjælp af Visual Studio](service-fabric-debugging-your-application.md)
