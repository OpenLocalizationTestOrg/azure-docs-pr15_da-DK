<properties
   pageTitle="Oversigt over Azure Service-strukturen pålidelig aktører KVSActorStateProvider konfigurationen | Microsoft Azure"
   description="Få mere at vide om at konfigurere Azure Service-strukturen med høj sikkerhed aktører af typen KVSActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfiguration af pålidelige aktører – KVSActorStateProvider
Du kan ændre standardkonfigurationen af KVSActorStateProvider ved at ændre den settings.xml-fil, der oprettes i Microsoft Visual Studio-pakke roden under mappen Config for den angivne Agent.

Azure Service strukturen runtime søger efter foruddefinerede Sektionsnavne i filen settings.xml og bruger konfigurationsværdier under oprettelse af de underliggende runtime-komponenter.

>[AZURE.NOTE] Kan **ikke** slette eller ændre sektion navnene på de følgende konfigurationer i filen settings.xml, der er oprettet i Visual Studio-løsning.

## <a name="replicator-security-configuration"></a>Konfiguration af Microsoft-sikkerhed
Microsoft sikkerhedskonfigurationer bruges til at sikre kommunikationskanalen, der bruges under replikering. Det betyder, at tjenester ikke kan se hinandens replikeringstrafik, at sikre, at dataene, som er meget tilgængelige også er sikker.
En tom sikkerhed konfiguration sektion forhindrer som standard, gentagelse sikkerhed.

### <a name="section-name"></a>Navnet på afsnit
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguration af Microsoft
Microsoft konfigurationer konfigurere Microsoft, der er ansvarlig for at foretage tilstanden Agent tilstand udbyder særdeles pålidelig.
Standardkonfiguration genereres af skabelonen Visual Studio og ønske. Dette afsnit taler om yderligere konfigurationer, der er tilgængelige til at finjustere af Microsoft.

### <a name="section-name"></a>Navnet på afsnit
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Af konfigurationsnavne

|Navn|Enhed|Standardværdi|Bemærkninger|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Sekunder|0.015|Tidsperiode, tilbage Microsoft på de sekundære venter efter at have modtaget en handling før afsendelse af en bekræftelse til primært. Andre godkendelser skal sendes til handlinger behandles i dette interval er sendt som et svar.|
|ReplicatorEndpoint|I/T.|Ingen standard – påkrævet parameter|IP-adresse og port, som den primære/sekundære af Microsoft skal bruge til at kommunikere med andre replikatorer i replikaen indstilles. Dette skal referere til et TCP ressource slutpunkt i tjenestemanifestet. Refererer til [Service manifest ressourcer](service-fabric-service-manifest-resources.md) , der kan få mere at vide om at definere slutpunkt ressourcer i tjenestemanifestet. |
|RetryInterval|Sekunder|5|Det tidsrum, hvorefter Microsoft igen sender en meddelelse Hvis det ikke modtager en bekræftelse for en handling.|
|MaxReplicationMessageSize|Byte|50 MB|Maksimumstørrelsen for gentagelse data, der kan overføres i en enkelt meddelelse.|
|MaxPrimaryReplicationQueueSize|Antal handlinger|1024|Maksimale antal handlinger i den primære kø. Handlingen er frigjort, når den primære Microsoft modtager en bekræftelse fra de sekundære replikatorer. Denne værdi skal være større end 64 og en potens på 2.|
|MaxSecondaryReplicationQueueSize|Antal handlinger|2048|Maksimale antal handlinger i sekundær køen. Handlingen er frigjort når du har foretaget tilstanden meget tilgængelige via brugerdata. Denne værdi skal være større end 64 og en potens på 2.|

## <a name="store-configuration"></a>Store konfiguration
Store konfigurationer bruges til at konfigurere det lokale lager, der bruges til at fastholde tilstanden, replikeres.
Standardkonfiguration genereres af skabelonen Visual Studio og ønske. Dette afsnit taler om yderligere konfigurationer, der er tilgængelige til at finjustere det lokale lager.

### <a name="section-name"></a>Navnet på afsnit
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Af konfigurationsnavne

|Navn|Enhed|Standardværdi|Bemærkninger|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Millisekunder|200|Angiver den maksimale "samling" interval for robust lokale lager anvendelser.|
|MaxVerPages|Antallet af sider|16384 som standard|Det maksimale antal version sider i lokalt store database. Den bestemmer det maksimale antal udestående transaktioner.|

## <a name="sample-configuration-file"></a>Eksempel på konfigurationsfil

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## <a name="remarks"></a>Bemærkninger

Parameteren BatchAcknowledgementInterval styrer ventetid for replikering. En værdi på '0' giver den laveste mulige forsinkelse på bekostning af overførselshastighed (som flere bekræftelsesmeddelelser skal sendes og behandles, hver indeholder færre godkendelser).
Jo større værdien for BatchAcknowledgementInterval, jo højere overordnede gentagelse overførselshastigheden, bekostning højere handlingen ventetid. Dette omsættes direkte til forsinkelse på transaktion anvendelser.
