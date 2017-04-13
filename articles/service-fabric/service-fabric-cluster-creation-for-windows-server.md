<properties
   pageTitle="Oprette og administrere en enkeltstående Azure Service strukturen klynge | Microsoft Azure"
   description="Oprette og administrere en Azure Service strukturen klynge på en hvilken som helst computer (fysisk eller virtuel) du kører Windows Server, uanset om det er lokalt eller i en hvilken som helst skyen."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>


# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Oprette og administrere en klynge, der kører på Windows Server

Du kan bruge Azure Service-strukturen til at oprette Service-strukturen klynger på en hvilken som helst virtuelle computere eller computere, der kører Windows Server. Det betyder, at du kan installere og køre tjenesten strukturen programmer i et miljø, der indeholder et sæt af forbundne Windows Server-computere, være den lokalt eller med en hvilken som helst cloud-udbyder. Tjenesten strukturen indeholder en installationspakke for at oprette Service-strukturen klynger kaldet pakken standalone Windows Server.

I denne artikel fører dig gennem trinnene til at oprette en klynge ved hjælp af den separate pakke til tjenesten strukturen lokalt, selvom den nemt kan tilpasses til et andet miljø som andre skyen udbydere.

>[AZURE.NOTE] Denne enkeltstående Windows Server pakke kan indeholde funktioner, der aktuelt findes i Vis udskrift og understøttes ikke til kommerciel brug. Du kan se på listen over funktioner, der er i Vis udskrift, i "Preview funktioner inkluderet i denne pakke". Du kan også [hente en kopi af den slutbrugerlicensaftale (EULA)](http://go.microsoft.com/fwlink/?LinkID=733084) nu.


<a id="getsupport"></a>
## <a name="get-support-for-the-service-fabric-standalone-package"></a>Få support til pakken Service-strukturen standalone

- Spørg community'et om tjenesten strukturen enkeltstående pakke til Windows Server i [Azure Service strukturen forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).

- Åbn en brugertilladelse for [Professional understøttelse af tjenesten struktur](http://support.microsoft.com/oas/default.aspx?prid=16146 ).  Lær mere om [Professionel Support fra Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).

<a id="downloadpackage"></a>
## <a name="download-the-service-fabric-standalone-package"></a>Hent pakken Service-strukturen enkeltstående


[Hent pakken enkeltstående for tjenesten strukturen for Windows Server 2012 R2 eller nyere](http://go.microsoft.com/fwlink/?LinkId=730690), som kaldes Microsoft.Azure.ServiceFabric.WindowsServer. &lt;version&gt;.zip.


I pakken, kan du se følgende filer:

|**Filnavn**|**Kort beskrivelse**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|.Cab-filen, der indeholder de binære filer, der er installeret på hver computer i klyngen.|
|ClusterConfig.Unsecure.DevCluster.json|En klynge eksempel konfigurationsfil, der indeholder indstillingerne for en usikker, tre-node, enkelt computer (eller virtuelt) udvikling klynge, herunder oplysningerne om hver node i klyngen. |
|ClusterConfig.Unsecure.MultiMachine.json|En klynge eksempel konfigurationsfil, der indeholder indstillingerne for en usikker, med flere maskine (eller virtuelt) klynge, herunder oplysninger til hver computer i klyngen.|
|ClusterConfig.Windows.DevCluster.json|En klynge eksempel konfigurationsfil, der indeholder alle indstillingerne for en sikker, tre-node, enkelt computer (eller virtuelt) udvikling klynge, herunder oplysningerne om hver node, der er i klyngen. Klyngen er sikret ved hjælp af [Windows-identiteter](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|En klynge eksempel konfigurationsfil, der indeholder alle indstillingerne for en sikker, med flere maskine (eller virtuelt) klynge ved hjælp af Windows sikkerhed, herunder oplysningerne om hver enkelt computer, der er i secure klynge. Klyngen er sikret ved hjælp af [Windows-identiteter](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|En klynge eksempel konfigurationsfil, der indeholder alle indstillingerne for en sikker, tre-node, enkelt computer (eller virtuelt) udvikling klynge, herunder oplysningerne om hver node i klyngen. Klyngen er beskyttet med x509 certifikater.|
|ClusterConfig.x509.MultiMachine.json|En klynge eksempel konfigurationsfil, der indeholder alle indstillingerne for sikker, med flere maskine (eller virtuelt) klynge, herunder oplysningerne om hver node i secure klynge. Klyngen er beskyttet med x509 certifikater.|
|EULA_ENU.txt|Licensvilkår for brug af Microsoft Azure Service strukturen enkeltstående Windows Server pakke. Du kan [hente en kopi af den slutbrugerlicensaftale (EULA)](http://go.microsoft.com/fwlink/?LinkID=733084) nu.|
|Vigtigt.txt|Et link til produktbemærkninger og grundlæggende installationsvejledning. Det er et undersæt af vejledningen i dette dokument.|
|CreateServiceFabricCluster.ps1|En PowerShell-script, som opretter den klynge ved hjælp af indstillingerne i ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|En PowerShell-script, der fjerner en klynge ved hjælp af indstillingerne i ClusterConfig.json.|
|ThirdPartyNotice.rtf |Varsling om software fra tredjepart, som findes i emballagen.|
|AddNode.ps1|En PowerShell-script til at føje en node til en eksisterende installeret klynge.|
|RemoveNode.ps1|En PowerShell-script til at fjerne en node fra en eksisterende installeret klynge.|
|CleanFabric.ps1|En PowerShell-script for at rydde en enkeltstående Service-strukturen installation fra den aktuelle computer. Tidligere installationer af MSI skal fjernes ved hjælp af deres egne tilknyttede uninstallers.|
|TestConfiguration.ps1|En PowerShell-script til at analysere infrastruktur som angivet i Cluster.json.|


## <a name="plan-and-prepare-your-cluster-deployment"></a>Planlægge og forberede din klynge installation
Udfør følgende trin, før du opretter din klynge.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Trin 1: Planlægge din klynge infrastruktur
Du er ved at oprette en tjeneste-strukturen klynge på computere, du ejer, så du kan beslutte, hvilke typer af fejl, du vil klynge for at klare. For eksempel, du har brug for Adskil power streger eller Internetforbindelser leveret til disse computere? Desuden overveje fysisk sikkerheden for disse computere. Hvor findes på computere, og hvem der skal have adgang til dem? Når du har foretaget disse beslutninger, kan du knytte maskiner logisk på de forskellige fejl domæner (se trin 4). Planlægning af produktionsklynger infrastrukturen er mere avanceret end til test klynger.

<a id="preparemachines"></a>
### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Trin 2: Forberede computere til de rette forudsætninger
Forudsætninger for hver computer, du vil føje til klyngen:

- Et minimum af 16 GB RAM anbefales.
- Et minimum af 40 GB ledig harddiskplads anbefales.
- En 4 core eller større CPU anbefales.
- Forbindelse til et sikkert netværk eller netværk for alle computere.
- Windows Server 2012 R2 eller Windows Server 2012 (du skal have [KB2858668](https://support.microsoft.com/kb/2858668) installeret).
- [.NET framework 4.5.1 eller højere](https://www.microsoft.com/download/details.aspx?id=40773), komplet installation.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- [RemoteRegistry tjeneste](https://technet.microsoft.com/library/cc754820) skal køre på alle computere.

Klyngeadministratoren installation og konfiguration af klyngen skal have [administratorrettigheder](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) på hver af maskiner. Du kan ikke installere Service-strukturen på et domænenavn fra domænecontrolleren.

### <a name="step-3-determine-the-initial-cluster-size"></a>Trin 3: Bestemme indledende klyngestørrelse
Hver node i en enkeltstående Service-strukturen klynge har Service-strukturen runtime udrulles og er medlem af klyngen. I et typisk produktionsmiljø er der en node per OS forekomst, (fysisk eller virtuel). Klyngestørrelsen bestemmes af virksomhedens behov. Skal du have en mindste klyngestørrelse af tre knuder (maskiner eller virtuelle maskiner).
Udviklingsformål, kan du har mere end én node på en given maskine. I et produktionsmiljø understøtter tjenesten strukturen kun én node per fysiske eller virtuelt.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Trin 4: Finde antallet fejl domæner og opgradere domæner
En *fejl domæne* (FD) er en fysisk enhed for fejl og er direkte relateret til datacentre fysisk infrastruktur. Et et domæne består af hardwarekomponenter (computere, parametre, netværk og mere), som deler for fejl fra ét sted. Selvom der ikke er nogen 1:1 tilknytning mellem et domæner og rack, kan løst taler hvert rack betragtes som et et domæne. Når du beslutter knuderne i din klynge, anbefales det, at noderne distribueres mellem mindst tre fejl domæner.

Når du angiver FDs i ClusterConfig.json, kan du vælge navnet på hver FD. Tjenesten strukturen understøtter hierarkiske FDs, så du kan afspejle dine infrastruktur topologi i dem.  For eksempel er følgende FDs gyldige:

- "faultDomain": "fd: / Room1/Rack1/COMPUTER1"
- "faultDomain": "fd: / FD1"
- "faultDomain": "fd: / Room1/Rack1/PDU1/M1"


En *opgradere domæne* (UD) er en logisk enhed af knuder. Under Service-strukturen orchestrated opgraderinger (enten en programmet opgradering eller en klynge opgradering) hentes alle noder i en UD til at udføre opgraderingen, mens knuder på andre UDs er stadig tilgængelige til at betjene anmodninger. De firmwareopgraderinger, du udfører på dine computere kan ikke angives UDs, så du skal gøre dem én maskine ad gangen.

Den nemmeste måde at synes om disse begreber er at overveje FDs som måleenheden for ikke-planlagt systemfejl og UDs som måleenheden for planlagt vedligeholdelse.

Når du angiver UDs i ClusterConfig.json, kan du vælge navnet på hver UD. For eksempel er følgende navne gyldige:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blå"

Se [der beskriver en tjeneste-strukturen klynge](service-fabric-cluster-resource-manager-cluster-description.md)kan finde mere detaljerede oplysninger om opgradering domæner og fejl domæner.

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Trin 5: Hent pakken Service-strukturen enkeltstående til Windows Server
[Hent pakken Service-strukturen enkeltstående til Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) og udpakke pakken, til en installation maskine, som ikke er en del af klyngen eller til en af de computere, der skal være en del af din klynge. Du kan omdøbe mappen Udpakket `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>
## <a name="create-your-cluster"></a>Oprette din klynge

Når du har gennemgået planlægning og forberedelse trinnene, er du klar til at oprette din klynge.

### <a name="step-1-modify-cluster-configuration"></a>Trin 1: Ændre klynge konfiguration
Klyngen er beskrevet i ClusterConfig.json. Du kan finde oplysninger om sektioner i denne fil [konfigurationsindstillinger for enkeltstående Windows klynge](service-fabric-cluster-manifest.md).
Åbne en af ClusterConfig.json filerne fra pakken, du har hentet og ændre de følgende indstillinger:

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

|**Denne indstilling**|**Beskrivelse**|
|-----------------------|--------------------------|
|**NodeTypes**|Node-typer giver dig mulighed at adskille din klyngenoder i forskellige grupper. En klynge skal have mindst én NodeType. Alle noder i en gruppe har følgende almindelige egenskaber: <br> **Navn** - dette er navnet på node. <br>**Slutpunkt porte** – disse er forskellige navngivet slutpunkter (porte), der er knyttet til denne nodetype. Du kan bruge en hvilken som helst portnummer, som du ønsker, så længe de ikke er i konflikt med andet i denne manifestfil og kan ikke allerede er i brug af et andet program, der kører på den maskine/VM. <br> **Egenskaber for placering** – disse beskrives egenskaberne for denne nodetype, du bruger som placeringen begrænsninger for systemets tjenester eller dine tjenester. Disse egenskaber er brugerdefinerede nøgle/værdi-par, som giver ekstra metadata til en given node. Eksempler på Egenskaber for knude ville være om noden har en harddisk eller grafikkort, antallet af omdrejningsaksler i dets harddisk, kerner og andre fysiske egenskaber. <br> **Kapacitet** - Node kapacitet Definer navn og mængde af en bestemt ressource, der har en bestemt node tilgængelig for forbrug. For eksempel kan en node definere, at det har kapacitet for en metrikværdi kaldet "MemoryInMb" og at det har 2048 MB, der er tilgængelige som standard. Disse kapacitet bruges på kørselstidspunktet til at sikre, at tjenester, der kræver særlig mængder ressourcer er placeret på de noder, der har disse ressourcer, der er tilgængelige i de nødvendige beløb.<br>**IsPrimary** - Hvis du har mere end én NodeType, der er defineret sikre, at kun én er indstillet til primære med værdi *sandt*, hvilket er hvor systemets tjenester køres. Alle andre node-typer skal du vælge værdi *Falsk*|
|**Noder**|Dette er detaljerne for hver af de noder, der er en del af klynge (node af type, navn på node, IP-adresse, et domæne og opgradering domænet for noden). Computerne, du vil klynge skal oprettes på har du brug for at blive nævnt her med deres IP-adresser. <br> Hvis du bruger den samme IP-adresse for alle noder, derefter oprettes en én boks klynge, som du kan bruge til testformål. Brug ikke én boks klynger til implementering af fremstilling arbejdsmængder.|


### <a name="step-2-run-the-testconfiguration-script"></a>Trin 2: Køre scriptet TestConfiguration

Scriptet TestConfiguration tester infrastrukturen, som defineret i cluster.json at sikre, at de nødvendige tilladelser er tildelt, ressourcerne er forbundet med hinanden og andre attributter er defineret, så installationen kan fuldføres. Det er som en mini analyse af bedste fremgangsmåder. Vi fortsætter med at føje flere valideringer til dette værktøj over tid for at gøre det mere robust.

Dette script kan køres på en hvilken som helst computer, der har administratoradgang til alle de computere, der er angivet som noder i klynge konfigurationsfil. Den computer, der kører dette script på behøver ikke at være en del af klyngen.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3-run-the-create-cluster-script"></a>Trin 3: Køre scriptet Opret klynge
Når du har ændret indstillingerne klynge i JSON dokumentet og har tilføjet alle nodeoplysninger, Kør klynge oprettelse af *CreateServiceFabricCluster.ps1* PowerShell-script fra mappen pakke og overføre i stien til filen JSON konfiguration. Når dette er fuldført, skal du acceptere den slutbrugerlicensaftale (EULA).

Dette script kan køres på en hvilken som helst computer, der har administratoradgang til alle de computere, der er angivet som noder i klynge konfigurationsfil. Den computer, der kører dette script på behøver ikke at være en del af klyngen.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] Loggene installation er tilgængelig lokalt på den VM/maskine, du har kørt CreateServiceFabricCluster PowerShell på. De er i undermappen DeploymentTraces under den mappe, hvor du kørte PowerShell-kommando. Få vist Hvis Service-strukturen blev installeret korrekt på en computer, du kan finde de installerede filer i mappen C:\ProgramData og kan ses i FabricHost.exe og Fabric.exe processer kører i Jobliste.

### <a name="step-4-connect-to-the-cluster"></a>Trin 4: Oprette forbindelse til klyngen

Se [oprette forbindelse til sikker klynge Service-strukturen](service-fabric-connect-to-secure-cluster.md)for at oprette forbindelse til en sikker klynge.

For at oprette forbindelse til en usikker klynge, skal du køre følgende PowerShell-kommando:

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>Trin 5: Få vist Service-strukturen Explorer

Nu kan du oprette forbindelse til klynge med Service-strukturen Explorer enten direkte fra en af maskiner med http://localhost:19080/Explorer/index.html eller fra en fjernplacering med http://<*IPAddressofaMachine*>: 19080/Explorer/index.html.



## <a name="add-and-remove-nodes"></a>Tilføje og fjerne noder

Du kan tilføje eller fjerne noder til din enkeltstående Service-strukturen klynge efter virksomhedens behov ændres. Se [tilføje eller fjerne noder til en tjeneste-strukturen enkeltstående klynge](service-fabric-cluster-windows-server-add-remove-nodes.md) have en detaljeret vejledning.


## <a name="remove-a-cluster"></a>Fjerne en klynge

Hvis du vil fjerne en klynge, Kør *RemoveServiceFabricCluster.ps1* PowerShell-script fra mappen pakke og overføre i stien til filen JSON konfiguration. Du kan eventuelt angive en placering til loggen af sletningen.

Dette script kan køres på en hvilken som helst computer, der har administratoradgang til alle de computere, der er angivet som noder i klynge konfigurationsfil. Den computer, der kører dette script på behøver ikke at være en del af klyngen.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>
## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetridata, der indsamles, og Sådan fravælger du det

Som standard indsamler produktet telemetri på Service-strukturen brugen til at forbedre produktet. Den bedste praksis Analyzer, der kører som en del af konfigurationen kontrollerer forbindelsen til [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Hvis det ikke er tilgængelig, mislykkes konfigurationen, medmindre du fravælge telemetri.

  1. Telemetri pipeline forsøger at overføre følgende data til [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) én gang hver dag. Det er en bedste evne Overfør og har ingen indvirkning på klyngefunktionaliteten. Telemetri sendes kun fra noden, der kører sekundære manager primære. Ingen andre noder send telemetri.

  2. Telemetri består af følgende:

-            Antallet af tjenester
-            Antallet af ServiceTypes
-            Antallet af programmer
-            Antallet af ApplicationUpgrades
-            Antallet af FailoverUnits
-            Antallet af InBuildFailoverUnits
-            Antallet af UnhealthyFailoverUnits
-            Antal replikaer
-            Antallet af InBuildReplicas
-            Antallet af StandByReplicas
-            Antallet af OfflineReplicas
-            CommonQueueLength
-            QueryQueueLength
-            FailoverUnitQueueLength
-            CommitQueueLength
-            Antallet af knuder
-            IsContextComplete: SAND/FALSK
-            ClusterId: Dette er et tilfældigt oprettet for hver klynge GUID
-            ServiceFabricVersion
-             IP-adressen på den virtuelle eller computer, hvorfra telemetri er overført


Hvis du vil deaktivere telemetri, Tilføj følgende *Egenskaber* i din klynge config: *enableTelemetry: falsk*.

<a id="previewfeatures"></a>
## <a name="preview-features-included-in-this-package"></a>Vis funktioner i denne pakke

Ingen.

>[AZURE.NOTE] Med den nye [GA version af enkeltstående klynge til Windows Server (version 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), kan du opgradere din klynge til fremtidige versioner manuelt eller automatisk. Da denne funktion ikke er tilgængelige i preview-versioner, skal du oprette en klynge ved hjælp af GA-versionen og overføre dine data og programmer fra preview klynge. Vær klar til flere oplysninger om denne funktion.


## <a name="next-steps"></a>Næste trin
- [Konfigurationsindstillinger for enkeltstående Windows klynge](service-fabric-cluster-manifest.md)
- [Tilføje eller fjerne noder til en enkeltstående Service-strukturen klynge](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Oprette en enkeltstående Service-strukturen klynge med Azure VM'er, der kører Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Sikre en enkeltstående klynge på Windows ved hjælp af Windows-sikkerhed](service-fabric-windows-cluster-windows-security.md)
- [Sikre en enkeltstående klynge på Windows ved hjælp af X509 certifikater](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
