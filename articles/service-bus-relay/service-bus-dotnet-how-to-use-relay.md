<properties
    pageTitle="Sådan bruger du Service Bus relay med .NET | Microsoft Azure"
    description="Lær, hvordan du bruger Azure Service Bus relay-tjenesten til at oprette forbindelse to programmer, der er hostet på forskellige placeringer."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Sådan bruger du Azure Service Bus relay-tjenesten

I denne artikel beskrives, hvordan du bruger Service Bus relay-tjenesten. Eksemplerne er skrevet i C# og bruge Windows Communication Foundation (WCF) API med filtypenavne, der er indeholdt i samlingen Service Bus. Få vist oversigten [Service Bus videresendes messaging](service-bus-relay-overview.md) kan finde flere oplysninger om Service Bus relay.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>Hvad er Service Bus relay?

[Service Bus *relay* ](service-bus-relay-overview.md) -tjenesten giver dig mulighed at opbygge hybrid programmer, der kører i både en Azure datacenter og din egen lokale virksomhedsmiljø. Service Bus relay muliggør dette ved at gøre det muligt at vise sikkert Windows Communication Foundation (WCF) tjenester, der er placeret i et virksomhedsmiljø virksomhedens netværk i offentlige skyen, uden at skulle åbne en firewallforbindelse og uden krav om forstyrrende ændringer i en virksomhedens netværksinfrastruktur.

![Relay begreber](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Service Bus relay gør det muligt at host WCF-tjenester i din eksisterende virksomhedsmiljø. Du kan derefter uddelegere lytter efter indgående sessioner og anmodninger om til disse WCF-tjenester til tjenesten Service Bus i Azure, der kører. Dette gør det muligt at få vist disse tjenester til programkode, der kører i Azure eller til mobile medarbejdere eller ekstranet partner miljøer. Service Bus gør det muligt at sikkert styre, hvem der kan få adgang til disse tjenester på et detaljerede niveau. Det er en effektiv og sikker måde at vise programmets funktionalitet og data fra dit eksisterende virksomhedsløsninger og drage fordel af den fra skyen.

I denne artikel viser, hvordan du bruger Service Bus relay til at oprette en WCF-webtjeneste, der vises ved hjælp af en TCP-kanal binding, der implementerer en sikker samtale mellem to parter.

## <a name="create-a-service-namespace"></a>Oprette et service navneområde

Hvis du vil begynde at bruge Service Bus relay i Azure, skal du først oprette et navneområde. Et navneområde indeholder en angiver området objektbeholder til at håndtere Service Bus ressourcer i dit program.

Sådan oprettes et service navneområde:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Få pakken Service Bus NuGet

[Tjenesten Bus NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus) er den nemmeste måde at få Service Bus API og for at konfigurere dit program med alle Service Bus afhængigheder. For at installere pakken NuGet i dit projekt skal du gøre følgende:

1.  I Solution Explorer, skal du højreklikke på **referencer**, og klik derefter på **Administrer NuGet pakker**.
2.  Søg efter "Service Bus", og vælg elementet, **Microsoft Azure Service Bus** . Klik på **Installer** for at fuldføre installationen, og derefter lukke dialogboksen følgende:

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Bruge Service Bus til at vise og bruge en SOAP-webtjeneste med TCP

For at få vist en eksisterende SOAP WCF-webtjeneste til eksterne forbrug, skal du foretage ændringer til tjenestebindinger og adresser. Dette kan kræve ændringer i din konfigurationsfil eller det kan kræve kodeændringer, afhængigt af hvordan du har oprettet og konfigureret dine WCF-tjenester. Bemærk, at WCF tillader, at du har flere netværksslutpunkter over den samme tjeneste, så du kan bevare de eksisterende interne slutpunkter under tilføjelse af Service Bus slutpunkter for ekstern adgang på samme tid.

I denne opgave, kan du oprette en simpel WCF-webtjeneste og føje en Service Bus lytter til den. Denne øvelse antages det et vist kendskab til Visual Studio, og derfor gennemgå ikke alle oplysninger om oprettelse af et projekt. I stedet fokuserer det på koden.

Gør følgende for at konfigurere dit miljø, før du starter disse trin:

1.  Oprette et console-program, der indeholder to projekter, "Klient" og "Tjeneste" i løsningen i Visual Studio.
2.  Føje Microsoft Azure Service Bus NuGet pakken til begge projekter. Denne pakke tilføjer alle de nødvendige samling referencer til dine projekter.

### <a name="how-to-create-the-service"></a>Sådan oprettes tjenesten

Opret først tjenesten sig selv. En WCF-webtjeneste består af mindst tre adskilte dele:

-   Definition af en kontrakt, der beskriver, hvilke meddelelser er udvekslet, og hvad handlinger er skal aktiveres.
-   Implementering af nævnte kontrakt.
-   Vært, der er vært for WCF-tjenesten og viser flere slutpunkter.

Kodeeksempler i dette afsnit omfatte hver af disse komponenter.

Kontrakten definerer en enkelt handling `AddNumbers`, der lægger to tal og returnerer resultatet. Den `IProblemSolverChannel` grænseflade gør det muligt for klienten til at administrere nemmere proxy levetid. Oprette en sådan grænseflade betragtes som bedste fremgangsmåde. Det er en god ide at sætte denne kontrakt definition i en separat fil, så du kan referere til denne fil fra både "Klient" og "Service" projekter, men du kan også kopiere koden til begge projekter.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Med kontrakt på plads er implementeringen trivial.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Konfigurere en tjenestevært fra et program

Med kontrakten og implementering på plads, kan du nu hoste tjenesten. Vært forekommer i et [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) objekt, der tager sig af administration af forekomster af tjenesten og hoster de slutpunkter, lytte til meddelelser. Følgende kode konfigurerer tjenesten med både et almindelige lokalt slutpunkt og et Service Bus slutpunkt til at illustrere udseende, side om side af interne og eksterne slutpunkter. Erstat streng *navneområde* med din navneområde navn og *yourKey* med nøglen SAS, som du hentede i det forrige trin i konfigurationen.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

I eksemplet, kan du oprette to slutpunkter, som er på den samme kontrakt implementering. En er lokale og en projiceres gennem Service Bus. De tre vigtigste forskelle mellem dem er bindinger; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) til det lokale domæne og [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) for Service Bus slutpunktet og adresserne. Det lokale slutpunkt har en lokal network adresse med et entydigt port. Service Bus slutpunkt har en adresse på slutpunkt består af strengen `sb`, navneområdenavnet på din og stien "Problemløser." Dette resulterer i URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identificere tjenesteslutpunkt som et slutpunkt Service Bus TCP med et fuldt kvalificeret eksterne DNS-navn. Hvis du markerer den kode, der erstatter pladsholderne i den `Main` funktionen af **tjenesteprogrammet,** har du en funktionelle tjeneste. Hvis du vil din tjeneste lytte udelukkende på Service Bus, skal du fjerne angivelsen lokale slutpunkt.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Konfigurere en tjenestevært i App.config-filen

Du kan også konfigurere værten ved hjælp af App.config-filen. Tjenesten vært kode i dette tilfælde vises i det næste eksempel.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Slutpunkt definitioner flytte til App.config-filen. Pakken NuGet har allerede tilføjet en række definitioner til App.config-filen, som er påkrævede konfiguration filtypenavne for Service Bus. I følgende eksempel, som er præcis svarer til den forrige kode, skal vises direkte under **system.serviceModel** elementet. Denne kodeeksempel antages det, at din project C# navneområde hedder **Service**.
Erstat pladsholderne med din tjeneste Bus service navneområde og SAS nøgle.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Når du har foretaget disse ændringer, tjenesten starter som den gjorde tidligere, men med to live slutpunkter: én lokale og én lytter i skyen.

### <a name="create-the-client"></a>Oprette klienten

#### <a name="configure-a-client-programmatically"></a>Konfigurere en klient fra et program

Hvis du vil bruge tjenesten, kan du oprette en WCF-klient ved hjælp af et [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) objekt. Service Bus bruger en token-baserede sikkerhedsmodel, der er implementeret ved hjælp af SAS. Klassen [en TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) repræsenterer en sikkerhed token udbyder med indbyggede factory metoder, der returnerer nogle kendte token udbydere. I følgende eksempel bruges metoden [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) til at håndtere acquisition af det relevante SAS token. Navn og nøgle er dem, der fås fra portalen, som beskrevet i forrige afsnit.

Først skal referere til eller kopiere den `IProblemSolver` sammentrække kode fra tjenesten til klientprojektet.

Erstat derefter koden i den `Main` metode til at klienten, igen erstatte pladsholderteksten med din tjeneste Bus navneområdet og SAS nøgle.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Du kan nu oprette klienten og service skal køre dem (køre tjenesten først), og klienten ringer til tjenesten og udskriver **9**. Du kan køre klienten og serveren på forskellige computere, endda på tværs af netværk, og kommunikationen fungerer stadig. Klient-kode kan også køre i skyen eller lokalt.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Konfigurere en klient i App.config-filen

Følgende kode viser, hvordan du konfigurerer klienten ved hjælp af App.config-filen.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Slutpunkt definitioner flytte til App.config-filen. I følgende eksempel, som er den samme som den kode, der er angivet tidligere, skal vises direkte under **system.serviceModel** elementet. Her, som før, skal du erstatte pladsholderne med din tjeneste Bus navneområde og SAS nøgle.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus relay-tjenesten, skal du følge disse links for at få mere at vide.

- [Service Bus videresendes SMS oversigt](service-bus-relay-overview.md)
- [Azure Service Bus arkitektonisk oversigt](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Hente Service Bus eksempler fra [Azure eksempler][] , eller se [Oversigt over Service Bus eksempler][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Azure eksempler]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [Oversigt over Service Bus eksempler]: ../service-bus-messaging/service-bus-samples.md