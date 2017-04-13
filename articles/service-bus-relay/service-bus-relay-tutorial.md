<properties 
    pageTitle="Tjenesten Bus Relay selvstudium | Microsoft Azure"
    description="Oprette en Service Bus klient, program og bruger tjenesten Bus Relay-tjenesten."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Tjenesten Bus Relay selvstudium

Dette selvstudium beskriver, hvordan du opretter en simpel Service Bus-klientprogrammet og tjenesten ved hjælp af funktionerne Service Bus "relay". Du kan finde en tilsvarende selvstudium, der bruger Service Bus [formidlet messaging](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging) [Service Bus formidlet Messaging .NET selvstudium](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Samarbejde med dette selvstudium giver dig en forståelse af de trin, der er påkrævet for at oprette et Service Bus klient og tjeneste til computeren. Som deres WCF-versionerne er en tjeneste en konstruktion, der viser en eller flere slutpunkter, hver især viser en eller flere operationer. Slutpunkt til en tjeneste angiver en adresse, hvor tjenesten kan findes, en binding, der indeholder de oplysninger, som en klient skal kommunikere med tjenesten, og en kontrakt, der definerer de funktioner, som tjenesten dens klienter. Primære forskellen mellem en WCF- og en Service Bus tjeneste er, at slutpunktet, der vises i skyen i stedet for lokalt på din computer.

Når du arbejder via rækkefølge af emner i dette selvstudium, har du en aktiv tjeneste, og en klient, der kan aktivere handlinger til tjenesten. Det første emne beskriver, hvordan du konfigurerer en konto. De næste trin beskriver, hvordan du definerer en tjeneste, der bruger en kontrakt, hvordan du kan implementere tjenesten og hvordan du konfigurerer tjenesten kode. De også beskriver, hvordan vært og køre tjenesten. Den tjeneste, der er oprettet er selv hostet og klient og tjeneste kører på den samme computer. Du kan konfigurere tjenesten ved hjælp af kode eller en konfigurationsfil.

De sidste tre trin beskriver, hvordan du opretter en klientprogrammet, konfigurere klientprogrammet, og oprette og bruge en klient, der kan få adgang til funktionaliteten i vært.

Alle emner i dette afsnit forudsætter, at du bruger Visual Studio som udviklingsmiljøet.

## <a name="sign-up-for-an-account"></a>Tilmelde sig en konto

Det første trin er at oprette et navneområde, og du får fat i en delt Access signatur (SAS) nøgle. Et navneområde indeholder en programmet grænse for hvert program fremvises Service Bus. En SAS nøgle genereres automatisk af systemet, når der oprettes et service navneområde. Kombinationen af tjenesten navneområde og SAS nøgle yder legitimationsoplysninger for tjenesten Bus til at godkende adgang til et program.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Definere en WCF-tjenesten kontrakt til brug med Service Bus

Kontrakten angiver, hvilke handlinger (Web service terminologien for metoder eller funktioner) tjenesten understøtter. Kontrakter oprettes ved at definere en C++, C# eller Visual Basic-grænseflade. Hver enkelt metode i grænsefladen svarer til en bestemt servicehandling. Hver grænseflade skal have attributten [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) anvendt på, og hver handling skal have attributten [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) anvendt på. Hvis en metode i en grænseflade, der indeholder attributten [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) ikke har attributten [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) , vises ikke denne metode. Koden for disse opgaver er angivet i eksemplet følge fremgangsmåden. Større finde en beskrivelse af kontrakter og -tjenester, [designe og implementere tjenester](https://msdn.microsoft.com/library/ms729746.aspx) i dokumentationen til WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Oprette en Service Bus kontrakt med en grænseflade

1. Åbne Visual Studio som administrator ved at højreklikke på programmet i menuen **Start** og vælge **Kør som administrator**.

2. Oprette et nyt console programmet projekt. Klik på fanen **filer** og vælge **Ny**og derefter klikke på **projekt**. I dialogboksen **Nyt projekt** , klik på **Visual C#** (Hvis **Visual C#** ikke vises, se under **Andre sprog**). Klik på skabelonen **Console-program** , og kald den **EchoService**. Klik på **OK** for at oprette projektet.

    ![][2]

3. Installer Service Bus NuGet pakken. Denne pakke tilføjer automatisk referencer til Service Bus biblioteker, samt WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) er det navneområde, som giver automatisk adgang til de grundlæggende funktioner i WCF. Service Bus bruger mange af objekterne og attributter i WCF til at definere servicekontrakter.

    I Solution Explorer, højreklik på løsningen, og klik derefter på **Administrer NuGet pakker til løsning**. Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus`. Sørg for, at projektnavnet er valgt i feltet **versioner** . Klik på **Installer**, og Accepter vilkår for anvendelse.

    ![][3]

3. I Solution Explorer skal du dobbeltklikke på filen Program.cs for at åbne den i editoren skal, hvis den ikke allerede er åben.

4. Tilføj følgende ved hjælp af sætninger øverst i filen:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Ændre navneområdenavnet på fra dens standardnavnet på **EchoService** til **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Dette selvstudium bruger C#-navneområde **Microsoft.ServiceBus.Samples**, som er navneområdet af kontrakttypen administrerede, der bruges i konfigurationsfil trinnet [Konfigurer WCF-klienten](#configure-the-wcf-client) . Du kan angive en hvilken som helst navneområde, du vil, når du opretter i dette eksempel dog fungerer selvstudiet ikke, medmindre du derefter ændre navneområder af kontrakten og servicere tilsvarende, i programmets konfigurationsfil. Navneområdet angivet i filen App.config skal være den samme som det navneområde, der er angivet i dine C#-filer.

1. Umiddelbart efter den `Microsoft.ServiceBus.Samples` navneområde definition, men i navneområdet, definere en ny brugergrænseflade navngivet `IEchoContract` og anvende den `ServiceContractAttribute` attribut til brugergrænsefladen med **http://samples.microsoft.com/ServiceModel/Relay/**navneområde værdien. Værdien navneområde er forskellig fra det navneområde, du bruger i hele omfanget af din kode. I stedet, bruges værdien navneområde som et entydigt id for denne kontrakt. Angiver navneområdet eksplicit forhindrer navneområde standardværdien i at blive føjet til kontrakten.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] Typisk indeholder navneområdet service kontrakt et naming farveskema, der indeholder versionsoplysninger. Herunder versionsoplysninger i navneområdet service kontrakt aktiverer tjenester til at isolere større ændringer ved at definere en ny tjeneste kontrakt med et nyt navneområde og udsætte på et nyt slutpunkt. På denne måde kan klienter fortsætte med at bruge den gamle kontrakt uden at skulle opdateres. Versionsoplysninger kan bestå af en dato eller et build tal. Du kan finde yderligere oplysninger finder [Service versionsstyring](http://go.microsoft.com/fwlink/?LinkID=180498). Med henblik på dette selvstudium indeholder den naming ordning af tjenesten kontrakt navneområdet ikke versionsoplysninger.

1. I den `IEchoContract` brugergrænseflade, erklære en metode til handlingen enkelt den `IEchoContract` kontrakt Fremviser i grænsefladen og anvende den `OperationContractAttribute` attribut til den metode, du vil vise som en del af den offentlige Service Bus kontrakt.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Lige efter den `IEchoContract` brugergrænseflade definition, erklære en kanal, der nedarver fra begge `IEchoContract` og også til den `IClientChannel` brugergrænseflade, som vist her:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    En kanal er WCF-objektet som vært og klient overføre oplysninger til hinanden. Senere, skal du skrive programkode mod kanalen til ekko oplysninger mellem de to programmer.

1. Klik på **Opret løsning** fra menuen **Build** eller tryk på **Ctrl + Shift + B** for at bekræfte nøjagtigheden af dit arbejde hidtil.

### <a name="example"></a>Eksempel

Følgende kode viser en grundlæggende brugergrænseflade, der definerer en Service Bus kontrakt.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nu hvor grænsefladen er oprettet, kan du implementere grænsefladen.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implementere WCF kontrakten bruge Service Bus

Oprettelse af en Service Bus relay kræver, at du først opretter kontrakt, der er defineret ved hjælp af en grænseflade. Du kan finde flere oplysninger om oprettelse af brugergrænsefladen i det forrige trin. Næste trin er at implementere grænsefladen. Dette omfatter oprettelse af en klasse, kaldet `EchoService` , der implementerer den brugerdefinerede `IEchoContract` interface. Når du implementerer grænsefladen, kan du derefter konfigurere grænsefladen ved hjælp af en App.config konfigurationsfil. Af konfigurationsfilen indeholder nødvendige oplysninger til programmet, som navnet på tjenesten, navnet på kontrakten, og typen af protokollen, der bruges til at kommunikere med Service Bus. Den kode, der bruges til disse opgaver er angivet i eksemplet følge fremgangsmåden. Se [Implementere tjenesten kontrakter](https://msdn.microsoft.com/library/ms733764.aspx) mere generelle oplysninger om, hvordan du implementere en tjeneste kontrakt, i dokumentationen til WCF.

1. Oprette en ny klasse med navnet `EchoService` umiddelbart efter definitionen af den `IEchoContract` interface. Den `EchoService` klasse implementerer den `IEchoContract` interface. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Svarer til andre interface-installationer, du kan implementere definitionen i en anden fil. Dog til dette selvstudium implementeringen er placeret i den samme fil som interfacedefinitionen og `Main` metode.

1. Anvend [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) attributten til den `IEchoContract` interface. Attributten angiver navnet på tjenesten og navneområde. Når du har gjort det, skal du på `EchoService` klasse vises på følgende måde:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementere den `Echo` metode, der er defineret i den `IEchoContract` brugergrænseflade i den `EchoService` klasse. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Klik på **Generer**, og klik derefter på **Opret løsning** for at bekræfte nøjagtigheden af dit arbejde.

### <a name="to-define-the-configuration-for-the-service-host"></a>Til at definere konfigurationen til Tjenesteværten for

1. Konfigurationsfilen minder meget om en WCF-konfigurationsfil. Den indeholder navnet på tjenesten, slutpunkt (det vil sige på det sted, som Service Bus fremviser for klienter og værter til at kommunikere med hinanden) og binding (typen af protokollen, der bruges til at kommunikere). Den største forskel er, at dette konfigurerede service slutpunkt refererer til en [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) binding, som ikke er en del af .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) er et af de bindinger, der er defineret af Service Bus.

1. I **Solution Explorer**, skal du dobbeltklikke på App.config-filen for at åbne den i Visual Studio editor.

2. I den `<appSettings>` element, Erstat pladsholderne med navnet på din tjeneste navneområde og Sikkerhedstilknytningerne centrale, du kopierede i et tidligere trin. 

1. I den `<system.serviceModel>` mærker, tilføje en `<services>` element. Du kan angive flere Service Bus programmer i en enkelt konfigurationsfil. Dette selvstudium definerer derimod kun én.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. I den `<services>` element, tilføje en `<service>` element til at definere navnet på tjenesten.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. I den `<service>` element, angive placeringen af slutpunktskontrakt, samt typen binding for slutpunktet.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Slutpunktet definerer, hvor klienten søger efter værtsprogrammet. Senere, bruger selvstudiet dette trin til at oprette en URI, der viser fuldt værten gennem Service Bus. Bindingen erklærer, at vi bruger TCP som protokollen til at kommunikere med Service Bus.

1. Klik på **Opret løsning** for at bekræfte nøjagtigheden af dit arbejde fra menuen **Build** .

### <a name="example"></a>Eksempel

Følgende kode viser implementeringen af kontrakten.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Følgende kode viser grundlæggende formatet af App.config-filen, der er knyttet til Tjenesteværten for.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Vært og køre en grundlæggende webtjeneste til at registrere med Service Bus

Dette trin beskriver, hvordan du køre en grundlæggende Service Bus-tjeneste.

### <a name="to-create-the-service-bus-credentials"></a>Til at oprette Service Bus legitimationsoplysninger

1. I `Main()`, oprette to variabler i hvor du vil gemme navneområdet og Sikkerhedstilknytningerne centrale, der læses fra vinduet console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Tasten SAS bruges senere til at få adgang til projektet Service Bus. Navneområdet for den er sendt som en parameter til `CreateServiceUri` til at oprette en tjeneste URI.

4. Ved hjælp af et [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) objekt erklære, at du bruger en SAS nøgle som typen legitimationsoplysninger. Tilføj følgende kode lige efter den kode, der er tilføjet i det sidste trin.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Oprette en basisadresse for tjenesten

1. Følge den kode, du tilføjede i det sidste trin, oprette en `Uri` forekomst for basisadressen til tjenesten. Denne URI angiver Service Bus farveskema, navneområdet og stien til grænsefladen til webtjenester.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" er en forkortelse for Service Bus farveskema, og angiver, at vi bruger TCP som protokollen. Dette er også tidligere angivet i konfigurationsfil, når [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) er angivet som bindingen.
    
    I dette selvstudium URI er `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Oprette og konfigurere Tjenesteværten for

1. Angiv tilstanden forbindelse til `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Tilstanden connectivity beskrives protokollen tjenesten bruger til at kommunikere med Service Bus HTTP- eller TCP. Ved hjælp af standardindstillingen `AutoDetect`, tjenesten forsøger at oprette forbindelse til tjenesten Bus via TCP, hvis det er tilgængeligt, og HTTP, hvis TCP ikke er tilgængelig. Bemærk, at dette er forskellig fra protokollen tjenesten angiver for klientkommunikation. Denne protokol bestemmes af den binding, der bruges. En tjeneste kan for eksempel bruge [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) binding, der angiver, at dens slutpunkt (vises på Service Bus) kommunikerer med klienter via HTTP. Den samme tjeneste kan angive **ConnectivityMode.AutoDetect** , så tjenesten kommunikerer med Service Bus over TCP.

1. Opret Tjenesteværten for, ved hjælp af URI oprettede tidligere i dette afsnit.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Tjenesteværten for er WCF-objektet, der starter tjenesten. Her kan du sende dem typen tjeneste, du vil oprette (en `EchoService` type), og også til den adresse, som du vil vise tjenesten.

1. Føje referencer til [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) og [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx)øverst i filen Program.cs.

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Tilbage i `Main()`, Konfigurer slutpunktet for at aktivere offentlig adgang.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Dette trin oplyser Service Bus, som du kan finde dit program offentligt ved at undersøge Service Bus ATOM-feed for projektet. Hvis du angiver **DiscoveryType** til **private**, vil en klient stadig kunne få adgang til tjenesten. Tjenesten vil ikke vises, når der søges navneområdet Service Bus. I stedet skulle klienten kender slutpunkt stien på forhånd.

1. Anvende service legitimationsoplysninger til tjenesten slutpunkterne defineret i App.config-filen:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Som angivet i det forrige trin, kan du har defineret flere tjenester og slutpunkter i konfigurationsfil. Hvis du havde, vil denne kode gennemgang konfigurationsfil og Søg efter hver slutpunkt, som det skal gælde for dine legitimationsoplysninger. I dette selvstudium har konfigurationsfilen dog kun et slutpunkt.

### <a name="to-open-the-service-host"></a>Åbne værten service

1. Åbn tjenesten.

    ```
    host.Open();
    ```

1. Informer brugeren, tjenesten kører, og viser, hvordan du Luk tjenesten.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Luk Tjenesteværten for, når du er færdig.

    ```
    host.Close();
    ```

1. Tryk på **Ctrl + Shift + B** for at oprette projektet.

### <a name="example"></a>Eksempel

I følgende eksempel indeholder servicekontrakten og implementering fra de forrige trin i selvstudiet, og er vært for tjenesten i et console til computeren. Samle følgende i en eksekverbar fil med navnet EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Oprette en WCF-klient til kontrakten

Næste trin er at oprette en grundlæggende Service Bus-klientprogrammet og definere den kontrakt du implementerer i efterfølgende trin. Bemærk, at mange af disse trin ligner trinnene bruges til at oprette en tjeneste: definere en kontrakt, redigerer en App.config fil, ved hjælp af legitimationsoplysninger til at oprette forbindelse til tjenesten Bus, og så videre. Den kode, der bruges til disse opgaver er angivet i eksemplet følge fremgangsmåden.

1. Opret et nyt projekt i den aktuelle Visual Studio-løsning for klienten ved at gøre følgende:
    1. Højreklik på den aktuelle løsning (ikke projektet) i Solution Explorer, i den samme løsning, der indeholder tjenesten, og klik på **Tilføj**. Klik derefter på **Nyt projekt**.
    2. Klik på i dialogboksen **Tilføj ny projekt** **Visual C#** (Hvis **Visual C#** ikke vises, se under **Andre sprog**), Vælg skabelonen, **Console-program** , og kald den **EchoClient**.
    3. Klik på **OK**.
<br />

1. I Solution Explorer skal du dobbeltklikke på filen Program.cs i **EchoClient** projektet for at åbne den i editoren skal, hvis den ikke allerede er åben.

1. Ændre navneområdenavnet fra dens standardnavnet på `EchoClient` til `Microsoft.ServiceBus.Samples`.

1. Installere [Service Bus NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Højreklik på **EchoClient** projektet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**. Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus`. Klik på **Installer**, og Accepter vilkår for anvendelse.

    ![][3]

1. Tilføje en `using` erklæringen for navneområdet [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) i filen Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Føje definitionen af tjenesten kontrakt til navneområdet, som vist i følgende eksempel. Bemærk, at denne definition er identisk med den definition, der bruges i **tjenesten** projektet. Du bør tilføje denne kode øverst på den `Microsoft.ServiceBus.Samples` navneområde.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Tryk på **Ctrl + Shift + B** for at opbygge klienten.

### <a name="example"></a>Eksempel

Følgende kode viser den aktuelle status for filen Program.cs i EchoClient projektet.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurere WCF-klient

I dette trin skal oprette du en App.config-filen til en grundlæggende klientprogrammet, der har adgang til tjenesten oprettet tidligere i dette selvstudium. Denne fil App.config definerer kontrakt, indbinding og navnet på slutpunktet. Den kode, der bruges til disse opgaver er angivet i eksemplet følge fremgangsmåden.

1. Dobbeltklik på **App.config** for at åbne filen i Visual Studio editoren i project **EchoClient** i Solution Explorer.

2. I den `<appSettings>` element, Erstat pladsholderne med navnet på din tjeneste navneområde og Sikkerhedstilknytningerne centrale, du kopierede i et tidligere trin.

1. I elementet system.serviceModel, tilføje en `<client>` element.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Dette trin erklærer, at du definerer en WCF-typografi-klientprogrammet.

1. I den `client` element, Definer navn, kontrakt og binding type for slutpunktet.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Dette trin definerer navnet på slutpunktet, den kontrakt, der er defineret i tjenesten, og fakultet, klientprogrammet bruger TCP til at kommunikere med Service Bus. Navnet på bruges i næste trin til at sammenkæde denne slutpunktskonfiguration med URI-tjenesten.

1. Klik på **filer**, derefter **Gem alt**.

## <a name="example"></a>Eksempel

Følgende kode viser App.config-filen til ekkoet-klienten.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implementere WCF-klienten for at ringe Service Bus

I dette trin skal implementere du en grundlæggende klientprogrammet, der har adgang til den tjeneste, du har oprettet tidligere i dette selvstudium. Svarer til tjenesten, klienten udfører mange af de samme handlinger til at få adgang til tjenesten Bus:

1. Angiver connectivity tilstanden.
1. Opretter den URI, som finder tjenesten vært.
1. Definerer legitimationsoplysningerne.
1. Gælder legitimationsoplysningerne for forbindelsen.
1. Åbner forbindelsen.
1. Udfører program-specifikke opgaver.
1. Afbryder forbindelsen.

En af de vigtigste forskelle er dog, klientprogrammet bruger en kanal til at oprette forbindelse til tjenesten Bus, mens tjenesten bruger et opkald til **ServiceHost**. Den kode, der bruges til disse opgaver er angivet i eksemplet følge fremgangsmåden.

### <a name="to-implement-a-client-application"></a>At implementere en klientprogrammet

1. Angiv tilstanden connectivity til **automatisk genkendelse af**. Føj følgende kode i den `Main()` metode til at **EchoClient** programmet.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definere variabler for at holde værdierne for den tjeneste navneområde og SAS nøgle, der læses fra konsollen.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Oprette den URI, der definerer placeringen af værten i projektet Service Bus.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Oprette objektet legitimationsoplysninger for din tjeneste navneområde slutpunkt.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Oprette den kanal fabrik, der indlæser konfigurationen er beskrevet i App.config-filen.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    En kanal fabrik er en WCF-objekt, der opretter en kanal, som de tjeneste og klienten programmer kommunikere.

1. Anvende Service Bus legitimationsoplysninger.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Opret og Åbn kanal til tjenesten.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Skriv den grundlæggende brugergrænseflade og funktionalitet for ekkoet.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Bemærk, at koden bruger forekomsten af objektet kanal som en proxy for tjenesten.

1. Luk kanalen, og Luk fabriksindstillingerne.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Til at køre programmer

1. Tryk på **Ctrl + Shift + B** for at opbygge løsningen. Dette opbygger både klientprojektet og projektet tjeneste, du oprettede i de forrige trin.

2. Før du kører klientprogrammet, skal du sikre dig, at tjenesteprogrammet kører. I Solution Explorer i Visual Studio, skal du højreklikke på **EchoService** løsningen, og klik derefter på **Egenskaber**.

3. Klik på **Start projekt**i dialogboksen løsning egenskaber og derefter klikke på knappen **flere Start projekter** . Kontrollere **EchoService** vises øverst på listen. 

4. Angiv feltet **Action** for både **EchoService** og **EchoClient** projekter til at **starte**.

    ![][5]

5. Klik på **Projektafhængigheder**. Vælg **EchoClient**i boksen **projekter** . Kontrollér, at **EchoService** er markeret i feltet **afhænger af** .

    ![][6]

6. Klik på **OK** for at lukke dialogboksen **Egenskaber** .

7. Tryk på **F5** for at køre begge projekter.

8. Begge console vinduer Åbn og beder dig om navneområdenavnet. Tjenesten skal køres først, så Indtast navneområdet i **EchoService** console-vinduet, og tryk derefter på **Enter**.

9. Derefter skal bliver du bedt om SAS nøglen. Tasten SAS og tryk på ENTER.

    Her er eksempel output fra vinduet console. Bemærk, at værdierne angivet her er for eksempel kun.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    Tjenesteprogrammet udskrives, til vinduet console den adresse, som den lytter, som det fremgår i følgende eksempel.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Angiv de samme oplysninger, du tidligere har angivet for tjenesteprogrammet i vinduet **EchoClient** console. Følg de forrige trin for at angive de samme service navneområde og SAS nøgleværdier for klientprogrammet.

11. Når du har angivet disse værdier, klienten åbnes en kanal til tjenesten og beder dig om at angive noget tekst, som det ses i eksemplet nedenfor console output.

    `Enter text to echo (or [Enter] to exit):` 

    Skriv noget tekst, hvis du vil sende til tjenesteprogrammet, og tryk på Enter. Denne tekst der sendes til tjenesten via handlingen ekkoet tjenesten og vises i vinduet console som i følgende eksempel output.

    `Echoing: My sample text`

    Klientprogrammet modtager den returnerede værdi af den `Echo` handling, som er den oprindelige tekst, og udskriver den til dens console-vindue. Følgende er eksempel output fra klient console-vinduet.

    `Server echoed: My sample text`

12. Du kan fortsætte med at sende SMS-beskeder fra klienten til tjenesten på denne måde. Når du er færdig, skal du trykke på Enter i vinduerne klient og tjeneste console for at afslutte begge programmer.

## <a name="example"></a>Eksempel

I følgende eksempel viser, hvordan du opretter en klientprogrammet, hvordan du kan ringe til handlinger for tjenesten og hvordan du lukker klienten, når handlingen opkaldet er fuldført.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Næste trin

Dette selvstudium viste, hvordan du opretter en Service Bus klient, program og tjenesten ved hjælp af funktionerne Service Bus "relay". Du kan finde en lignende selvstudium, der bruger Service Bus [Messaging](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging) [Service Bus formidlet Messaging .NET selvstudium](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Hvis du vil vide mere om Service Bus, under følgende emner.

- [Service Bus SMS oversigt](../service-bus-messaging/service-bus-messaging-overview.md)
- [Service Bus grundlæggende](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus arkitektur](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
