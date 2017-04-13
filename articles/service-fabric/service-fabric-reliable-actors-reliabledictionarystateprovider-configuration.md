<properties
   pageTitle="Oversigt over Azure Service-strukturen pålidelig aktører ReliableDictionaryActorStateProvider konfigurationen | Microsoft Azure"
   description="Få mere at vide om at konfigurere Azure Service-strukturen med høj sikkerhed aktører af typen ReliableDictionaryActorStateProvider."
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
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfiguration af pålidelige aktører – ReliableDictionaryActorStateProvider
Du kan ændre standardkonfigurationen af ReliableDictionaryActorStateProvider ved at ændre filen settings.xml oprettes i Visual Studio-pakke roden under mappen Config for den angivne Agent.

Azure Service strukturen runtime søger efter foruddefinerede Sektionsnavne i filen settings.xml og bruger konfigurationsværdier under oprettelse af de underliggende runtime-komponenter.

>[AZURE.NOTE] Kan **ikke** slette eller ændre sektion navnene på de følgende konfigurationer i filen settings.xml, der er oprettet i Visual Studio-løsning.

Der er også globale indstillinger, der påvirker konfigurationen af ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Global konfiguration

Den globale konfiguration er angivet i klynge manifestet for klynge i afsnittet KtlLogger. Det kan konfigurationen af den delte log placering og størrelse samt de globale hukommelse begrænsninger, der bruges af logføring af. Bemærk, at ændringer i klynge manifestet påvirker alle tjenester, der bruger ReliableDictionaryActorStateProvider og pålidelig med høj sikkerhed tjenester.

Klynge manifestet er en enkelt XML-fil, der indeholder indstillinger og konfigurationer, der gælder for alle noder og tjenester i klyngen. Filen er typisk kaldet ClusterManifest.xml. Du kan se klyngen manifest for din klynge ved hjælp af kommandoen Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Af konfigurationsnavne

|Navn|Enhed|Standardværdi|Bemærkninger|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Kilobyte|8388608|Mindste antal KB at fordele i kernetilstand til logføring Skriv bufferen hukommelse puljen. Denne hukommelsespulje bruges til cachelagring tilstandsoplysninger før skrivning til disk.|
|WriteBufferMemoryPoolMaximumInKB|Kilobyte|Ingen grænse|Maksimal tekststørrelse, skrive logføring af bufferen hukommelse puljen kan vokse.|
|SharedLogId|GUID|""|Angiver et entydigt GUID, der skal bruges til at identificere standard delte logfilen bruges af alle pålidelige tjenester på alle noder i klyngen, der ikke angiver SharedLogId i deres bestemte tjenestekonfiguration. Hvis SharedLogId er angivet, skal derefter SharedLogPath også angives.|
|SharedLogPath|Fuldt kvalificeret stinavn|""|Angiver den fulde sti, hvor den delte logfil bruges af alle pålidelige tjenester på alle noder i klyngen, der ikke angiver SharedLogPath i deres bestemte tjenestekonfiguration. Men hvis SharedLogPath er angivet, derefter SharedLogId skal også angives.|
|SharedLogSizeInMB|Megabyte|8192|Angiver antallet MB ledig plads på statisk allokeres til den delte log. Værdien skal være større eller 2048.|

### <a name="sample-cluster-manifest-section"></a>Eksempel klynge manifestafsnit
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Bemærkninger
Logføring af har en global samling af allokeret fra ikke-sideinddelt kernehukommelse, der er tilgængelige for alle pålidelige tjenester på en node til cachelagre tilstand data, før skrevet til dedikeret loggen, der er knyttet til pålidelig service replikaen hukommelse. Gruppestørrelsen styres af indstillingerne for WriteBufferMemoryPoolMinimumInKB og WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB angiver både den oprindelige størrelse af denne hukommelse samling og den laveste størrelse, gruppen hukommelse kan formindske. WriteBufferMemoryPoolMaximumInKB har den højeste størrelse, gruppen hukommelse kan vokse. Hver pålidelig service replika, der er åben kan øge størrelsen på hukommelse puljen med et system fastsat beløb op til WriteBufferMemoryPoolMaximumInKB. Hvis der er flere krav om hukommelse fra hukommelse puljen, end der er tilgængelig, forsinkes anmodninger om hukommelse, indtil hukommelse er tilgængelig. Derfor Hvis Skriv bufferen hukommelse puljen er for lille til en bestemt konfiguration kan derefter ydeevnen lide.

Indstillingerne for SharedLogId og SharedLogPath bruges altid sammen til at definere den GUID og en placering til den delte standardplacering for logfilen for alle noder i klyngen. Standard delte loggen bruges til alle pålidelige tjenester, der ikke angiver indstillingerne i settings.xml for den specifikke tjeneste. Bedste ydeevne, skal delte logfiler placeres på diske, der anvendes kun for den delte logfil til at reducere konflikt.

SharedLogSizeInMB angiver mængden diskplads at reservere for alle noder standard delte logon.  SharedLogId og SharedLogPath behøver ikke at være angivet i rækkefølge for SharedLogSizeInMB skal angives.

## <a name="replicator-security-configuration"></a>Konfiguration af Microsoft-sikkerhed
Microsoft sikkerhedskonfigurationer bruges til at sikre kommunikationskanalen, der bruges under replikering. Det betyder, at tjenester ikke kan se hinandens gentagelse trafik, sikre, at de data, som er meget tilgængelige er også sikre.
En tom sikkerhed konfiguration sektion forhindrer som standard, gentagelse sikkerhed.

### <a name="section-name"></a>Navnet på afsnit
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguration af Microsoft
Microsoft konfigurationer bruges til at konfigurere den af Microsoft, der er ansvarlig for at foretage tilstanden Agent tilstand udbyder særdeles pålidelig ved replikering og bevare tilstanden lokalt.
Standardkonfiguration genereres af skabelonen Visual Studio og ønske. Dette afsnit taler om yderligere konfigurationer, der er tilgængelige til at finjustere af Microsoft.

### <a name="section-name"></a>Navnet på afsnit
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Af konfigurationsnavne

|Navn|Enhed|Standardværdi|Bemærkninger|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Sekunder|0.015|Tidsperiode, tilbage Microsoft på de sekundære venter efter at have modtaget en handling før afsendelse af en bekræftelse til primært. Andre godkendelser skal sendes til handlinger behandles i dette interval er sendt som et svar.||
|ReplicatorEndpoint|I/T.|Ingen standard – påkrævet parameter|IP-adresse og port, som den primære/sekundære af Microsoft skal bruge til at kommunikere med andre replikatorer i replikaen indstilles. Dette skal referere til et TCP ressource slutpunkt i tjenestemanifestet. Refererer til [Service manifest ressourcer](service-fabric-service-manifest-resources.md) , der kan få mere at vide om at definere slutpunkt ressourcer i tjenestemanifestet. |
|MaxReplicationMessageSize|Byte|50 MB|Maksimumstørrelsen for gentagelse data, der kan overføres i en enkelt meddelelse.|
|MaxPrimaryReplicationQueueSize|Antal handlinger|8192|Maksimale antal handlinger i den primære kø. Handlingen er frigjort, når den primære Microsoft modtager en bekræftelse fra de sekundære replikatorer. Denne værdi skal være større end 64 og en potens på 2.|
|MaxSecondaryReplicationQueueSize|Antal handlinger|16384 som standard|Maksimale antal handlinger i sekundær køen. Handlingen er frigjort når du har foretaget tilstanden meget tilgængelige via brugerdata. Denne værdi skal være større end 64 og en potens på 2.|
|CheckpointThresholdInMB|MB|200|Mængden af log filplads, hvorefter tilstand er tildelt et kontrolpunkt.|
|MaxRecordSizeInKB|KB|1024|Største post størrelse, der kan skrive Replikatoren log. Denne værdi skal være et multiplum af 4 og større end 16.|
|OptimizeLogForLowerDiskUsage|Boolesk værdi|SAND|Hvis sand, konfigureret loggen, så der oprettes replikaens dedikeret logfil ved hjælp af en NTFS sparsefil. Dette sænker den faktiske diskplads for filen. Hvis falsk, oprettes filen med fast tildelinger, som understøtter skrive bedst ydeevne.|
|SharedLogId|GUID|""|Angiver et entydigt guid, der skal bruges til at identificere den delte logfil, der bruges sammen med denne replika. Typisk skal services ikke bruge denne indstilling. Men hvis SharedLogId er angivet, derefter SharedLogPath skal også angives.|
|SharedLogPath|Fuldt kvalificeret stinavn|""|Angiver den fulde sti, hvor delte logfilen for denne replika skal oprettes. Typisk skal services ikke bruge denne indstilling. Men hvis SharedLogPath er angivet, derefter SharedLogId skal også angives.|


## <a name="sample-configuration-file"></a>Eksempel på konfigurationsfil

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
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
Jo større værdien for BatchAcknowledgementInterval, jo højere overordnede gentagelse overførselshastigheden, bekostning højere handlingen ventetid. Dette oversætter direkte til forsinkelse på transaktion anvendelser.

Parameteren CheckpointThresholdInMB styrer mængden af plads på harddisken, som Microsoft kan bruge til at gemme oplysningerne i den replika dedikeret logfil. Øge dette til en højere værdi end standardplaceringen, kan det medføre konfigureres igen hurtigere når en ny replika er føjet til sættet. Dette er på grund af tilstandsoverførslen delvis, der skal udføres på grund af tilgængeligheden af flere oversigten over handlinger i logfilen. Dette kan potentielt øge gendannelse klokkeslættet for en replika efter nedbrud.

Hvis du indstiller OptimizeForLowerDiskUsage til sand, bliver logplads uberettiget klargjort, så aktive replikaer kan gemme flere tilstandsoplysninger i deres logfiler, mens inaktiv replikaer anvender mindre plads på harddisken. Det gør det muligt at være vært for flere replikaer på en node. Hvis du indstiller OptimizeForLowerDiskUsage til falsk, skrives tilstandsoplysninger hurtigere til loggene.

Indstillingen MaxRecordSizeInKB definerer den maksimale størrelse på en post, der kan skrives ved af Microsoft til logfilen. I de fleste tilfælde er 1024 KB post standardstørrelsen optimal. Men hvis tjenesten forårsager større dataelementer skal være en del statusoplysninger, derefter denne værdi være nødvendigt at øges. Der er ingen fordel i at MaxRecordSizeInKB mindre end 1024, som mindre poster bruger plads til den mindre post. Vi forventer, at denne værdi skal ændres kun i sjældne tilfælde.

Indstillingerne for SharedLogId og SharedLogPath bruges altid sammen til at foretage en tjeneste, bruge en separat delt logfil fra standard delte logfilen for noden. Så mange tjenester som muligt skal angive den samme delte log for bedste effektivitet. Delte logfiler skal placeres på diske, der anvendes udelukkende til delte logfilen til at reducere hoved bevægelse konflikt. Vi forventer, at disse værdier skal ændres kun i sjældne tilfælde.
