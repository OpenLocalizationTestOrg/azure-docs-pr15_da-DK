<properties
   pageTitle="Pålidelige Services WCF kommunikation stak | Microsoft Azure"
   description="Den indbyggede WCF kommunikation stak i tjenesten strukturen indeholder tjenesten client WCF kommunikation til pålidelig Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-baseret kommunikation stak for pålidelig tjenester
Den pålidelige tjenester, som gør det muligt for tjenesten forfattere at vælge den kommunikation stak, som de har brug for reparation. De kan slutte kommunikation stablede efter eget valg via **ICommunicationListener** returneres fra metoderne [CreateServiceReplicaListeners eller CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . , Som indeholder en implementering af kommunikation stablen baseret på Windows Communication Foundation (WCF) for tjenesten forfattere, der vil bruge WCF-baseret kommunikation.

## <a name="wcf-communication-listener"></a>WCF kommunikation lytteren
WCF-specifikke implementeringen af **ICommunicationListener** leveres af klassen **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** .

Føjes sig har vi en tjeneste kontrakt af typen`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Vi kan oprette en WCF kommunikation lytter i tjenesten på følgende måde.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Skrive klienter til WCF kommunikation stablen
Til at skrive klienter mulighed for at kommunikere med tjenester ved hjælp af WCF, indeholder rammerne **WcfClientCommunicationFactory**, som er WCF-specifikke implementering af [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

WCF-kommunikationskanal kan åbnes fra **WcfCommunicationClient** oprettet af **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klient-kode kan bruge **WcfCommunicationClientFactory** sammen med **WcfCommunicationClient** som implementerer **ServicePartitionClient** for at se tjenesteslutpunkt og kommunikere med tjenesten.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] Standard ServicePartitionResolver antages det, at klienten kører i samme klynge som tjenesten. Hvis det vil sige ikke er tilfældet, oprette et ServicePartitionResolver objekt og overføre i klynge Forbindelsesslutpunkterne.

## <a name="next-steps"></a>Næste trin
* [Fjernprocedurekald med pålidelig Services remoting](service-fabric-reliable-services-communication-remoting.md)

* [Web API med OWIN i pålidelige Services](service-fabric-reliable-services-communication-webapi.md)

* [Sikring af kommunikation til pålidelig Services](service-fabric-reliable-services-secure-communication.md)
