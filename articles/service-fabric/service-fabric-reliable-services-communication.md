<properties
   pageTitle="Oversigt over pålidelige Services-kommunikation | Microsoft Azure"
   description="Oversigt over pålidelige Services kommunikation modellen, herunder åbne lyttere på tjenester, løse slutpunkter og kommunikation mellem tjenester."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Sådan bruger du pålidelig Services kommunikationen API'er

Azure Service-struktur som en platform er fuldstændigt uafhængig om kommunikation mellem tjenester. Alle protokoller og stakke er acceptable fra UDP til HTTP. Det er op til tjenesten udvikler til at vælge, hvordan tjenester skal kommunikere. Den pålidelige Services-program, som indeholder indbyggede kommunikation stakke samt API'er, som du kan bruge til at opbygge din brugerdefinerede kommunikationskomponenter. 

## <a name="set-up-service-communication"></a>Konfigurere tjenesten kommunikation

Pålidelige Services API bruger en enkel grænseflade for tjenesten kommunikation. For at åbne et slutpunkt for din tjeneste skal du blot implementere denne grænseflade:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Du kan derefter tilføje implementeringen kommunikation lytteren ved at returnere det i en tjeneste-baserede klasse metode Tilsidesæt.

For uden status tjenester:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

For med høj sikkerhed tjenester:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

I begge tilfælde skal returnere du en samling af lyttere. Dette gør det muligt for dine tjenester til lytter på flere slutpunkter, da potentielt ved hjælp af forskellige protokoller, ved hjælp af flere lyttefunktioner. For eksempel kan du have en HTTP lytteren og en separat WebSocket lytter. Hver lytteren får et navn, og den deraf følgende samling af *navn: adresse* par repræsenteres som en JSON-objekt, når en klient anmoder om lytte adresserne for en forekomst af tjenesten eller en partition.

Returnerer en samling af ServiceInstanceListeners i en uden status tjeneste, ændringen. En ServiceInstanceListener indeholder en funktion for at oprette en ICommunicationListener og giver den et navn. Ændringen returnerer en samling af ServiceReplicaListeners for med høj sikkerhed tjenester. Dette er en smule anderledes end dens uden status modstykke, fordi en ServiceReplicaListener har mulighed for at åbne en ICommunicationListener på sekundær replikaer. Ikke kun kan du bruge flere kommunikation lyttere i en tjeneste, men du kan også angive, hvilke lyttere acceptere mødeindkaldelser på sekundær replikaer, og hvilke lytter kun på primær replikaer.

For eksempel kan du få en ServiceRemotingListener, der tager RPC opkald kun på primær replikaer, og en anden, brugerdefinerede lytter, der tager Læs anmoder om på sekundær replikaer via HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Når du opretter flere lyttere til en tjeneste, have hver lytteren **skal** et entydigt navn.

Til sidst skal Beskriv de slutpunkter, der kræves til tjenesten i den [tjeneste manifestet](service-fabric-application-model.md) under afsnittet om slutpunkter.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Kommunikation lytteren kan få adgang til slutpunkt ressourcer til den fra den `CodePackageActivationContext` i den `ServiceContext`. Lytteren kan derefter starte lytter efter anmodninger, når den åbnes.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Slutpunkt ressourcer er fælles for hele service pakken, og de er tildelt af Service-strukturen, når pakken tjenesten er aktiveret. Flere service replikaer hostet i den samme ServiceHost kan dele den samme port. Det betyder, at kommunikation lytteren skal understøtte Portdeling. Der er den anbefalede måde at gøre dette for kommunikation lytteren at bruge partition-ID og replika/forekomst-ID, når der oprettes lytte adressen.

### <a name="service-address-registration"></a>Tjenesten adresse registrering

En tjeneste kaldet *Naming Service* kører på Service-strukturen klynger. Tjenesten navngive er en domæneregistrator for tjenester og deres adresser, som hver forekomst eller en kopi af tjenesten lytter på. Når den `OpenAsync` metode til en `ICommunicationListener` er fuldført, dens retur værdien bliver registreret i tjenesten navngive. Den returnerede værdi, der udgives i tjenesten navngive er en streng, hvor værdien kan være noget overhovedet. Denne strengværdi er det klienter får vist, når de beder om en adresse til tjenesten fra tjenesten navngive.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Tjenesten strukturen indeholder API'er, der gør det muligt for kunder og andre tjenester derefter bede om denne adresse ved navn på tjeneste. Det er vigtigt, da den tjeneste-adresse ikke er statisk. Tjenester flyttes rundt i klynge til ressource justering og tilgængelighed formål. Dette er den metode, gør det muligt for klienter mulighed for at løse lytte adressen for en tjeneste.

> [AZURE.NOTE] For en komplet gennemgang af, hvordan du kan skrive en `ICommunicationListener`, se [Service strukturen Web API-tjenester med OWIN vært for præsentation](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>Kommunikere med en tjeneste
Pålidelige Services API giver de følgende biblioteker til at skrive klienter, der kommunikerer med tjenester.

### <a name="service-endpoint-resolution"></a>Tjenesten slutpunkt opløsning
Det første trin til kommunikation med en tjeneste er at løse et slutpunktsadressen på den partition eller forekomst af den tjeneste, du vil tale med. Den `ServicePartitionResolver` utility klasse er en grundlæggende primitiv, der hjælper med at finde ud af slutpunktet til en tjeneste på kørselstidspunktet klienter. I tjenesten strukturen terminologi kaldes processen til at bestemme slutpunkt til en tjeneste for *tjenesten slutpunkt opløsning*.

Oprette forbindelse til tjenester i en klynge `ServicePartitionResolver` kan oprettes ved hjælp af standardindstillingerne. Dette er det anbefalede forbrug for de fleste situationer:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Oprette forbindelse til tjenester i en anden klynge en `ServicePartitionResolver` kan oprettes med et sæt af klynge gateway slutpunkter. Bemærk, at gatewayen slutpunkter lige forskellige slutpunkter til at oprette forbindelse til den samme klynge. Eksempel:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Du kan også `ServicePartitionResolver` kan angives en funktion til at oprette en `FabricClient` bruge internt: 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`er det objekt, der bruges til at kommunikere med tjenesten strukturen klynge i forskellige management handlinger på klyngen. Dette er nyttigt, når du vil have mere kontrol over, hvordan `ServicePartitionResolver` skal arbejde sammen med din klynge. `FabricClient`udfører cachelagring internt og koster normalt til at oprette, så det er vigtigt at genbruge `FabricClient` forekomster som muligt. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

En løs metode er derefter bruges til at hente adressen til en tjeneste eller en tjeneste partition for partitioneret tjenester.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

En adresse til tjenester kan fortolkes nemt ved hjælp af en `ServicePartitionResolver`, men mere arbejde er påkrævet for at sikre, at den oversatte adresse, der kan bruges korrekt. Kunden har behov for at registrere om forbindelse mislykkedes på grund af en midlertidig fejl og kan blive forsøgt igen (f.eks., tjenesten flyttes eller er ikke tilgængelig i øjeblikket), eller en permanent fejl (f.eks., tjenesten er blevet slettet eller den ønskede ressource findes ikke længere). Forekomster af tjenesten eller replikaer kan flyttes fra node til node når som helst af flere årsager. Adressen til at løse `ServicePartitionResolver` kan være forældede, når din klientkode forsøger at oprette forbindelse. I dette tilfælde igen skal klienten løse adressen igen. Give den forrige `ResolvedServicePartition` angiver, at resolveren skal prøve igen i stedet for blot Hent en cachelagrede adresse.

Typisk Klientkoden ikke skal arbejde med den `ServicePartitionResolver` direkte. Det er oprettet og overføres til kommunikation klient fabrikker i API'EN pålidelig tjenester. Virksomheder bruge resolveren internt til at generere et klientobjekt, der kan bruges til at kommunikere med tjenester.

### <a name="communication-clients-and-factories"></a>Kommunikation klienter og fabrikker

Biblioteket kommunikation factory implementerer et typisk fejl fejlhåndtering forsøg mønster, som gør det nemmere at forsøger forbindelser til løst service slutpunkter. Biblioteket factory er forsøg middel, mens du giver fejl programmer.

`ICommunicationClientFactory`definerer den grundlæggende brugergrænseflade, der er implementeret af en kommunikation klient fabrik, der giver klienter, der kan tale til en tjeneste-strukturen tjeneste. Implementering af CommunicationClientFactory, afhænger af kommunikation stablen bruges af tjenesten Service-strukturen hvor klienten ønsker at kommunikere. Pålidelige Services API indeholder en `CommunicationClientFactoryBase<TCommunicationClient>`. Dette giver en grundlæggende implementering af den `ICommunicationClientFactory` brugergrænsefladen og udfører opgaver, der er fælles for alle kommunikation stakke. (Disse opgaver omfatter ved hjælp af en `ServicePartitionResolver` til at bestemme tjenesteslutpunkt). Klienter implementere som regel den abstrakte CommunicationClientFactoryBase klasse for at håndtere logik, der er specifikke for kommunikation stablen.

Kommunikation klienten modtager en adresse lige og bruges til at oprette forbindelse til en tjeneste. Klienten kan bruge uanset det ønsker protocol.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

Klient-factory er primært ansvarlig for at oprette kommunikation klienter. For kunder, der ikke opretholde en fast forbindelse, som en HTTP-klient skal fabriksindstillingerne kun til at oprette og returnere klienten. Andre protokoller, der bevarer en fast forbindelse, som nogle binære protokoller, skal også valideres af factory til at bestemme eller ej forbindelsen skal oprettes igen.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Til sidst er en undtagelsesbehandler ansvarlige for at afgøre, hvilken handling skal udføre, når en undtagelse opstår. Undtagelser er inddelt i **retriable** og **ikke retriable**. 

 - **Ikke retriable** undtagelser få blot igen udløst tilbage til kalderen. 
 - **Retriable** undtagelser kategoriseres yderligere i **midlertidige** og **permanent**.
  - **Midlertidige** undtagelser er dem, kan blot forsøges uden igen løse adressen til slutpunkt. Disse indeholder forbigående netværksproblemer eller tjeneste fejlsvar end dem, der angiver adressen til slutpunkt ikke findes. 
  - **Ikke-forbigående** undtagelser er dem, der kræver service slutpunktsadressen, der skal løses igen. Dette omfatter undtagelser, som angiver tjenesteslutpunkt ikke kunne oprettes forbindelse, er der angiver tjenesten flyttet til en anden node. 

Den `TryHandleException` gør en beslutning om en given undtagelse. Hvis det **ikke ved,** hvad beslutninger kom om en undtagelse, at den skal returnere **Falsk**. Hvis det **ved,** hvad beslutning gøre, skal den angive resultatet i overensstemmelse hermed og returnere **Sand**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Lægge det hele sammen
Med en `ICommunicationClient`, `ICommunicationClientFactory`, og `IExceptionHandler` opbygget omkring en kommunikationsprotokol, en `ServicePartitionClient` er ombrydes det alle sammen og giver fejl fejlhåndtering og service partition adresse opløsning løkke rundt om disse komponenter.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Næste trin
 - Se et eksempel på HTTP-kommunikation mellem tjenester i et [eksempelprojekt på GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Fjernprocedurekald med pålidelig Services remoting](service-fabric-reliable-services-communication-remoting.md)

 - [Web API, der bruger OWIN i pålidelige Services](service-fabric-reliable-services-communication-webapi.md)

 - [WCF-kommunikation ved hjælp af pålidelig Services](service-fabric-reliable-services-communication-wcf.md)
