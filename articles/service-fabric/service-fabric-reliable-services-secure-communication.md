<properties
   pageTitle="Hjælp til sikker kommunikation til tjenester i tjenesten strukturen | Microsoft Azure"
   description="Oversigt over, hvordan du kan gennemføre sikker kommunikation med pålidelig tjenester, der kører i en Azure Service strukturen klynge."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Sikker kommunikation i Hjælp for services i Azure Service struktur

Sikkerhed er et af de vigtigste aspekter af kommunikation. Den pålidelige Services-program, som indeholder et par færdige kommunikation stakke og værktøjer, som du kan bruge til at forbedre sikkerheden. I denne artikel kan tale om, hvordan du forbedre sikkerheden, når du bruger tjenesten remoting og Windows Communication Foundation (WCF) kommunikation stablen.

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Beskytte en tjeneste, når du bruger tjenesten remoting

Vi bruger et eksisterende [eksempel](service-fabric-reliable-services-communication-remoting.md) , der forklarer, hvordan du konfigurerer remoting for pålidelig tjenester. For at sikre en tjeneste, når du bruger tjenesten remoting, skal du følge disse trin:

1. Oprette en grænseflade `IHelloWorldStateful`, der definerer de metoder, der er tilgængelige for remote procedure call på din tjeneste. Din tjeneste anvender `FabricTransportServiceRemotingListener`, som er angivet i den `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` navneområde. Dette er en `ICommunicationListener` implementering, der indeholder remoting funktioner.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Tilføj indstillinger for lytteren og sikkerhedslegitimationsoplysninger.

    Sørg for, at det certifikat, du vil bruge til at sikre kommunikationen tjenesten er installeret på alle noder i klyngen. Der er to måder, du kan angive indstillinger for lytteren og sikkerhedslegitimationsoplysninger:

    1. Give dem direkte i tjenestekode:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Give dem ved hjælp af en [config pakke](service-fabric-application-model.md):

        Tilføje en `TransportSettings` sektion i filen settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        I dette tilfælde den `CreateServiceReplicaListeners` metode ser sådan ud:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Hvis du tilføjer en `TransportSettings` sektion i filen settings.xml uden et præfiks `FabricTransportListenerSettings` indlæser alle indstillingerne fra denne sektion som standard.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         I dette tilfælde den `CreateServiceReplicaListeners` metode ser sådan ud:

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Når du kalder metoder på en sikker tjeneste ved hjælp af stablen remoting i stedet for ved hjælp af den `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasse, der skal oprettes en tjenesteproxy, skal du bruge `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Overføre i `FabricTransportSettings`, som indeholder `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Hvis Klientkoden kører som en del af en tjeneste, kan du indlæse `FabricTransportSettings` fra filen settings.xml. Oprette en TransportSettings sektion, der ligner tjenestekode, som vist tidligere. Foretage følgende ændringer til klient-kode:

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Hvis klienten ikke kører som en del af en tjeneste, kan du oprette en client_name.settings.xml fil på samme placering, hvor client_name.exe er. Opret derefter en TransportSettings sektion i den pågældende fil.

    Svarer til tjenesten, hvis du tilføjer en `TransportSettings` sektion uden et præfiks i klienten settings.xml/client_name.settings.xml `FabricTransportSettings` indlæser alle indstillingerne fra denne sektion som standard.

    Det er tilfældet, forenklet tidligere koden yderligere:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Beskytte en tjeneste, når du bruger en WCF-baseret kommunikation stak

Vi bruger et eksisterende [eksempel](service-fabric-reliable-services-communication-wcf.md) , der forklarer, hvordan du konfigurerer en WCF-baseret kommunikation stak for pålidelig tjenester. For at sikre en tjeneste, når du bruger en WCF-baseret kommunikation stak, skal du følge disse trin:

1. For tjenesten, du har brug for til at sikre kommunikationen lytteren WCF (`WcfCommunicationListener`), som du opretter. Du kan ændre den `CreateServiceReplicaListeners` metode.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. I-klienten, den `WcfCommunicationClient` klasse, der blev oprettet i det forrige [eksempel](service-fabric-reliable-services-communication-wcf.md) forbliver uændret. Men du har brug at tilsidesætte de `CreateClientAsync` metode til at `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Brug `SecureWcfCommunicationClientFactory` til at oprette en WCF kommunikation-klient (`WcfCommunicationClient`). Brug af klienten til at aktivere tjenesten metoder.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Næste trin

* [Web API med OWIN i pålidelige Services](service-fabric-reliable-services-communication-webapi.md)
