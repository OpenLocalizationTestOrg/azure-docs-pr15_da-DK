<properties
   pageTitle="Angive Service-strukturen service slutpunkter | Microsoft Azure"
   description="Hvordan til at beskrive slutpunkt ressourcer i en tjenestemanifestet, herunder hvordan du konfigurerer HTTPS slutpunkter"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="specify-resources-in-a-service-manifest"></a>Angive ressourcer i en tjenestemanifestet

## <a name="overview"></a>Oversigt

Tjenestemanifestet kan ressourcer, der bruges af tjenesten er erklæret/ændres uden at ændre den kompilerede kode. Azure Service-strukturen understøtter konfiguration af slutpunkt ressourcer til tjenesten. Du kan styre adgang til de ressourcer, der er angivet i tjenestemanifestet via SecurityGroup i programmanifestet. Erklæring af ressourcer, der gør det muligt for disse ressourcer skal ændres fra det øjeblik, hvilket betyder, at tjenesten ikke er nødvendigt at indføre en ny ordning, konfiguration. Skemadefinitionen for filen ServiceManifest.xml er installeret med Service-strukturen SDK og værktøjer til *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Slutpunkter

Når en slutpunkt ressource er defineret i tjenestemanifestet, tildeler Service-strukturen porte fra området reserveret programmet port, når en port ikke er angivet eksplicit. Se for eksempel på slutpunkt *ServiceEndpoint1* angivet i manifest kodestykket angivet efter dette afsnit. Desuden services kan også anmode om en bestemt port i en ressource. Tjenesten replikaer kører på forskellige klyngenoderne kan tildeles andre portnumre, mens kopier af en tjeneste, der kører på den samme node dele port. Tjenesten replikaer kan derefter bruge portene efter behov for gentagelse og lytte efter klient-anmodninger.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Referere til [konfiguration af med høj sikkerhed pålidelig Services](service-fabric-reliable-services-configuration.md) for at læse mere om refererer til slutpunkter fra Pakkeindstillinger config fil (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Eksempel: angive en HTTP slutpunkt for din tjeneste

Den følgende tjenestemanifestet definerer én TCP slutpunkt ressource og to HTTP slutpunkt ressourcer i den &lt;ressourcer&gt; element.

HTTP-slutpunkter er automatisk ACL ville ved Service struktur.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Eksempel: angive en HTTPS slutpunkt for din tjeneste

HTTPS-protokollen giver server-godkendelse og bruges også til kryptering af klient / server-kommunikation. For at aktivere HTTPS på din tjeneste strukturen tjeneste, Angiv protokollen, i den *ressourcer -> slutpunkter slutpunkt ->* sektion af tjenestemanifestet, som vist tidligere for slutpunktet *ServiceEndpoint3*.

>[AZURE.NOTE] En tjeneste protocol kan ikke ændres under opgradering af programmet, uden at det udgør en seneste ændre.


Her er et eksempel ApplicationManifest, som du vil indstille til HTTPS. Miniature for dit certifikat skal angives. EndpointRef er en reference til EndpointResource i ServiceManifest, hvortil du angive HTTPS-protokollen. Du kan tilføje mere end én EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
