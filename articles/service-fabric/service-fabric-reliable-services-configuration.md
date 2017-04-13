<properties
   pageTitle="Oversigt over Azure Service strukturerede pålidelig Services konfigurationen | Microsoft Azure"
   description="Få mere at vide om at konfigurere med høj sikkerhed pålidelig Services i Azure Service struktur."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="sumukhs"/>

# <a name="configure-stateful-reliable-services"></a>Konfigurere med høj sikkerhed pålidelig services

Der findes to typer konfigurationsindstillinger for pålidelig tjenester. Ét sæt er global for alle pålidelige tjenester i klyngen, mens andet sæt er specifikke for en bestemt pålidelig tjeneste.

## <a name="global-configuration"></a>Global konfiguration

Global pålidelig service konfigurationen er angivet i klynge manifestet for klynge i afsnittet KtlLogger. Det kan konfigurationen af den delte log placering og størrelse samt de globale hukommelse begrænsninger, der bruges af logføring af. Klynge manifestet er en enkelt XML-fil, der indeholder indstillinger og konfigurationer, der gælder for alle noder og tjenester i klyngen. Filen er typisk kaldet ClusterManifest.xml. Du kan se klyngen manifest for din klynge ved hjælp af kommandoen Get-ServiceFabricClusterManifest powershell.

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


## <a name="service-specific-configuration"></a>Bestemte tjenestekonfiguration
Du kan ændre med høj sikkerhed pålidelig Services standardkonfigurationer ved hjælp af konfigurationspakken (Config) eller service implementering (kode).

+ **Config** - konfiguration via pakken config opnås ved at ændre filen Settings.xml, som er oprettet i Microsoft Visual Studio-pakke roden under mappen Config for hver tjeneste i programmet på computeren.
+ **Kode** - konfiguration via kode opnås ved at oprette en ReliableStateManager ved hjælp af et ReliableStateManagerConfiguration objekt med de relevante indstillinger.

Som standard Azure Service strukturen runtime søger efter foruddefinerede Sektionsnavne i filen Settings.xml og bruger konfigurationsværdier under oprettelse af de underliggende runtime-komponenter.

>[AZURE.NOTE] Undlad at **slette sektion navnene på de følgende konfigurationer i Settings.xml fil, som er** oprettet i Visual Studio-løsning, medmindre du planlægger at konfigurere din tjeneste via kode.
Omdøbe config pakke eller sektion navnene kræver en ændring af koden ved konfiguration af ReliableStateManager.


### <a name="replicator-security-configuration"></a>Konfiguration af Microsoft-sikkerhed
Microsoft sikkerhedskonfigurationer bruges til at sikre kommunikationskanalen, der bruges under replikering. Det betyder, at tjenester ikke kan kunne se hinandens gentagelse trafik, at sikre, at dataene, som er meget tilgængelige også er sikker. En tom sikkerhed konfiguration sektion forhindrer som standard, gentagelse sikkerhed.

### <a name="default-section-name"></a>Standardnavnet sektion
ReplicatorSecurityConfig

>[AZURE.NOTE] Tilsidesætte parameteren replicatorSecuritySectionName til ReliableStateManagerConfiguration parametre for at ændre dette navn for sektionen, når du opretter ReliableStateManager for denne tjeneste.


### <a name="replicator-configuration"></a>Konfiguration af Microsoft
Microsoft konfigurationer konfigurere Microsoft, der er ansvarlig for at foretage med høj sikkerhed pålidelig tjenestens tilstand særdeles pålidelig ved replikering og bevare tilstanden lokalt.
Standardkonfiguration genereres af skabelonen Visual Studio og ønske. Dette afsnit taler om yderligere konfigurationer, der er tilgængelige til at finjustere af Microsoft.

### <a name="default-section-name"></a>Standardnavnet sektion
ReplicatorConfig

>[AZURE.NOTE] Tilsidesætte parameteren replicatorSettingsSectionName til ReliableStateManagerConfiguration parametre for at ændre dette navn for sektionen, når du opretter ReliableStateManager for denne tjeneste.


### <a name="configuration-names"></a>Af konfigurationsnavne
|Navn|Enhed|Standardværdi|Bemærkninger|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Sekunder|0.015|Tidsperiode, tilbage Microsoft på de sekundære venter efter at have modtaget en handling før afsendelse af en bekræftelse til primært. Andre godkendelser skal sendes til handlinger behandles i dette interval er sendt som et svar.|
|ReplicatorEndpoint|I/T.|Ingen standard – påkrævet parameter|IP-adresse og port, som den primære/sekundære af Microsoft skal bruge til at kommunikere med andre replikatorer i replikaen indstilles. Dette skal referere til et TCP ressource slutpunkt i tjenestemanifestet. Refererer til [Service manifest ressourcer](service-fabric-service-manifest-resources.md) , der kan få mere at vide om at definere slutpunkt ressourcer i en tjenestemanifestet. |
|MaxPrimaryReplicationQueueSize|Antal handlinger|8192|Maksimale antal handlinger i den primære kø. Handlingen er frigjort, når den primære Microsoft modtager en bekræftelse fra de sekundære replikatorer. Denne værdi skal være større end 64 og en potens på 2.|
|MaxSecondaryReplicationQueueSize|Antal handlinger|16384 som standard|Maksimale antal handlinger i sekundær køen. Handlingen er frigjort når du har foretaget tilstanden meget tilgængelige via brugerdata. Denne værdi skal være større end 64 og en potens på 2.|
|CheckpointThresholdInMB|MB|50|Mængden af log filplads, hvorefter tilstand er tildelt et kontrolpunkt.|
|MaxRecordSizeInKB|KB|1024|Største post størrelse, der kan skrive Replikatoren log. Denne værdi skal være et multiplum af 4 og større end 16.|
|MinLogSizeInMB|MB|0 (system fastsat)|Minimumstørrelse af transaktions logfilen. Loggen tillades ikke afkorte til en størrelse under denne indstilling. 0 angiver, at Microsoft bestemmer mindste logstørrelse. Øge denne værdi øger risikoen for at gøre delvise kopier og trinvise sikkerhedskopier siden chancer for relevante logposter, bliver afkortet er sænket.|
|TruncationThresholdFactor|Faktor|2|Bestemmer, hvilke størrelse af loggen, afkortning af udløses. Afkortning af grænseværdi bestemmes af MinLogSizeInMB multipliceret med TruncationThresholdFactor. TruncationThresholdFactor skal være større end 1. MinLogSizeInMB * TruncationThresholdFactor skal være mindre end MaxStreamSizeInMB.|
|ThrottlingThresholdFactor|Faktor|4|Bestemmer, hvilke størrelse af loggen, replikaen begynder at blive begrænset. Variere den benyttede grænseværdi for (i MB), bestemmes af Maks ((MinLogSizeInMB *ThrottlingThresholdFactor),(CheckpointThresholdInMB* ThrottlingThresholdFactor)). Variere den benyttede grænseværdi for (i MB) skal være større end afkortning af grænseværdi (i MB). Afkortning af grænseværdien (i MB) skal være mindre end MaxStreamSizeInMB.|
|MaxAccumulatedBackupLogSizeInMB|MB|800|Maks akkumulerede størrelse (i MB) af sikkerhedskopiering logfiler i en given sikkerhedskopiering log kæde. En trinvist sikkerhedskopiering anmodninger mislykkes, hvis den trinvise sikkerhedskopiering vil oprette en sikkerhedskopi logfil, der medfører akkumulerede sikkerhedskopiering loggene siden den relevante fulde sikkerhedskopiering skal være større end denne størrelse. I så fald skal brugeren tage en fuld sikkerhedskopi.|
|SharedLogId|GUID|""|Angiver et entydigt GUID, der skal bruges til at identificere den delte logfil, der bruges sammen med denne replika. Typisk skal services ikke bruge denne indstilling. Men hvis SharedLogId er angivet, derefter SharedLogPath skal også angives.|
|SharedLogPath|Fuldt kvalificeret stinavn|""|Angiver den fulde sti, hvor delte logfilen for denne replika skal oprettes. Typisk skal services ikke bruge denne indstilling. Men hvis SharedLogPath er angivet, derefter SharedLogId skal også angives.|
|SlowApiMonitoringDuration|Sekunder|300|Angiver overvågning intervallet for administrerede API-opkald. Eksempel: bruger angivet sikkerhedskopiering Tilbagekaldsfunktionen. Efter intervallet, sendes en advarsel sundhed rapport til sundhed Manager.|

### <a name="sample-configuration-via-code"></a>Eksempel konfiguration via kode
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Eksempel på konfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Bemærkninger
Ventetid for replikering BatchAcknowledgementInterval kontrolelementer. En værdi på '0' giver den laveste mulige forsinkelse på bekostning af overførselshastighed (som flere bekræftelsesmeddelelser skal sendes og behandles, hver indeholder færre godkendelser).
Jo større værdien for BatchAcknowledgementInterval, jo højere overordnede replikering overførselshastigheden, bekostning højere handlingen ventetid. Dette omsættes direkte til forsinkelse på transaktion anvendelser.

Værdien for CheckpointThresholdInMB styrer mængden af plads på harddisken, som Microsoft kan bruge til at gemme oplysningerne i den replika dedikeret logfil. Øge dette til en højere værdi end standardplaceringen, kan det medføre konfigureres igen hurtigere når en ny replika er føjet til sættet. Dette er på grund af tilstandsoverførslen delvis, der skal udføres på grund af tilgængeligheden af flere oversigten over handlinger i logfilen. Dette kan potentielt øge gendannelse klokkeslættet for en replika efter nedbrud.

Indstillingen MaxRecordSizeInKB definerer den maksimale størrelse på en post, der kan skrives ved af Microsoft til logfilen. I de fleste tilfælde er 1024 KB post standardstørrelsen optimal. Men hvis tjenesten forårsager større dataelementer skal være en del statusoplysninger, derefter denne værdi være nødvendigt at øges. Der er ingen fordel i at MaxRecordSizeInKB mindre end 1024, som mindre poster bruger plads til den mindre post. Vi forventer, at denne værdi skal ændres i kun sjældne tilfælde.

Indstillingerne for SharedLogId og SharedLogPath bruges altid sammen til at foretage en tjeneste, bruge en separat delt logfil fra standard delte logfilen for noden. Så mange tjenester som muligt skal angive den samme delte log for bedste effektivitet. Delte logfiler skal placeres på diske, der anvendes kun for den delte logfil til at reducere hoved bevægelse konflikt. Vi forventer, at denne værdi skal ændres i kun sjældne tilfælde.

## <a name="next-steps"></a>Næste trin
 - [Fejlfinding af programmets Service-strukturen i Visual Studio](service-fabric-debugging-your-application.md)
 - [Udviklerreference for pålidelig tjenester](https://msdn.microsoft.com/library/azure/dn706529.aspx)
