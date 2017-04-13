<properties 
pageTitle="Kommunikation til roller i Cloud Services | Microsoft Azure" 
description="Forekomster af rolle i Cloud Services kan have slutpunkter (http, https, tcp, udp) defineret for dem, der kommunikerer med uden eller mellem andre rolle forekomster." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Aktivere kommunikation efter rolle forekomster i azure

Skybaseret tjeneste roller kommunikere via interne og eksterne forbindelser. Eksterne forbindelser kaldes **input slutpunkter** , mens interne forbindelser kaldes **interne slutpunkter**. Dette emne beskrives, hvordan du ændrer [tjenestedefinitionen](cloud-services-model-and-package.md#csdef) for at oprette slutpunkter.


## <a name="input-endpoint"></a>Indtast slutpunkt
Indtast slutpunktet bruges, når du vil vise en port uden. Du angiver typen protocol og port af det slutpunkt, som gælder for begge de eksterne og interne porte for slutpunktet. Hvis du vil, kan du angive en anden interne port for slutpunktet med attributten [Lokalport](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

Indtast slutpunktet kan bruge følgende protokoller: **http, https, tcp, udp**.

For at oprette et input slutpunkt skal du føje **InputEndpoint** underordnet element til elementet **slutpunkter** for et websted eller arbejder rolle.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Forekomst input slutpunkt
Forekomst input slutpunkter ligner input slutpunkter men kan du knytte bestemte offentligt porte for hver enkelt rolle forekomst ved hjælp af port videresendelse på justering af belastning. Du kan angive en enkelt offentligt port eller en række porte.

Forekomst input slutpunktet kan kun bruge **tcp** eller **UDP-** protokollen.

For at oprette en forekomst input slutpunkt skal du føje **InstanceInputEndpoint** underordnet element til elementet **slutpunkter** for et websted eller arbejder rolle.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Interne slutpunkt
Interne slutpunkter er tilgængelige for forekomst-forekomst kommunikation. Port er valgfri, og hvis udelades, er tildelt en dynamisk port slutpunktet. Du kan bruge et portområde. Er der en grænse på fem interne slutpunkter per rolle.

Det interne slutpunkt kan bruge følgende protokoller: **http, tcp, udp, en hvilken som helst**.

For at oprette et internt input slutpunkt skal du føje **InternalEndpoint** underordnet element til elementet **slutpunkter** for et websted eller arbejder rolle.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Du kan også bruge et portområde.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Arbejder roller kontra Web roller

Der er en mindre forskel med slutpunkter, når du arbejder med både arbejder og web roller. Rollen web skal som minimum have en enkelt input slutpunktet ved hjælp af **http-** protokollen.


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Brug af .NET SDK til at få adgang til et slutpunkt
Biblioteket Azure administrerede indeholder metoder til rolle forekomster til at kommunikere på kørselstidspunktet. Fra kode i en forekomst af rolle, der kører, kan du hente oplysninger om eksistensen af andre rolle forekomster og deres slutpunkter samt oplysninger om den aktuelle rolle forekomst.

> [AZURE.NOTE] Du kan kun hente oplysninger om rolle forekomster, der kører i din skytjeneste og, definere mindst én interne slutpunkt. Du kan ikke hente data om rolle forekomster, der kører i en anden tjeneste.

Du kan bruge egenskaben [forekomster](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) til at hente forekomster af en rolle. Bruge [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) til at returnere en reference til den aktuelle rolle-forekomst, og derefter bruge egenskaben [rolle](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) til at returnere en reference til rollen selve.

Når du opretter forbindelse til en rolle forekomst fra et program gennem .NET SDK, er det relativt nemt at få adgang til slutpunktsoplysningerne. Når du har allerede forbindelse til en bestemt rolle-miljø, kan du hente porten på en bestemt slutpunkt med denne kode:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Egenskaben **forekomster** returnerer en samling af **RoleInstance** objekter. Denne samling altid indeholder den aktuelle forekomst. Hvis rollen ikke definerer et internt slutpunkt, omfatter samlingen den aktuelle forekomst, men ingen andre forekomster. Antallet af forekomster af rolle i samlingen være altid 1 i de tilfælde, hvor ingen interne slutpunkt er defineret for rollen. Hvis rollen definerer et internt slutpunkt, dens forekomster er synlig på kørselstidspunktet, og antallet forekomster i gruppen af websteder vil svarer til antallet af forekomster, der er angivet for rollen i tjenesten konfigurationsfil.

> [AZURE.NOTE] Biblioteket Azure administrerede giver ikke mulighed for at afgøre tilstanden for andre rolle forekomster, men du kan implementere sådanne sundhed bedømmelser selv hvis din tjeneste skal bruge denne funktion. Du kan bruge [Azure diagnosticering](cloud-services-dotnet-diagnostics.md) til at få oplysninger om at køre rolle forekomster.

For at finde ud af portnummeret til et internt slutpunkt på en forekomst af rolle, kan du bruge egenskaben [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) til at returnere en ordbog objekt, der indeholder slutpunkt navne og deres tilsvarende IP-adresser og porte. Egenskaben [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) returnerer den IP-adresse og port for et bestemt slutpunkt. Egenskaben **PublicIPEndpoint** returnerer port for et Indlæs afstemmes slutpunkt. IP-adresse del af egenskaben **PublicIPEndpoint** bruges ikke.

Her er et eksempel, der gentages rolle forekomster.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Her er et eksempel på en kollega rolle, der bliver slutpunktet vises gennem definitionen af tjenesten og begynder at lytte til forbindelser.

> [AZURE.WARNING] Denne kode fungerer kun for en udløst tjeneste. Når du kører i Azure beregne emulatoren, ignoreres service konfiguration elementer, som opretter direkte port slutpunkter (**InstanceInputEndpoint** elementer).

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Netværk trafikregler til at styre rolle kommunikation
Når du definerer interne slutpunkter, kan du tilføje netværk trafikregler (baseret på de slutpunkter, du har oprettet) til at styre, hvordan rolle forekomster kan kommunikere med hinanden. I følgende diagram vises nogle almindelige scenarier for at styre rolle kommunikation:

![Netværk trafik regler scenarier] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Netværk trafik regler scenarier")

Følgende kodeeksempel viser rolledefinitioner for de roller, der vises i det forrige diagram. Hver rolledefinition indeholder mindst én interne slutpunkt defineret:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Begrænsning af kommunikation mellem roller kan opstå i forbindelse med interne slutpunkterne for både fast og tildeles automatisk porte.

Som standard, når et internt slutpunkt er defineret, kan kommunikation flyde fra en rolle til interne slutpunktet på en rolle uden begrænsninger. Hvis du vil begrænse kommunikation, skal du føje et **NetworkTrafficRules** element til elementet **ServiceDefinition** i definitionsfil tjeneste.

### <a name="scenario-1"></a>Scenarie 1
Kun tillade netværkstrafik fra **WebRole1** til **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenarie 2
Kan kun netværkstrafik fra **WebRole1** **WorkerRole1** og **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenarie 3
Kan kun netværkstrafik fra **WebRole1** **WorkerRole1**og **WorkerRole1** til **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario 4
Kan kun netværkstrafik fra **WebRole1** til **WorkerRole1**, **WebRole1** til **WorkerRole2**og **WorkerRole1** til **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Du kan finde en XML-skemareference til de elementer, der bruges over [her](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Næste trin
Læs mere om skybaseret tjeneste [model](cloud-services-model-and-package.md).