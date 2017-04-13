<properties
    pageTitle="Automatisere Service-strukturen programadministration ved hjælp af PowerShell | Microsoft Azure"
    description="Installere, opgradere, teste og fjerne tjenesten strukturen programmer ved hjælp af PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatisere programmet livscyklus ved hjælp af PowerShell

Mange aspekter af [tjenesten strukturen programmet supportlivscyklus](service-fabric-application-lifecycle.md) kan automatisk.  Denne artikel beskrives, hvordan du kan bruge PowerShell til at automatisere almindelige opgaver i forbindelse med installation, opgradering, fjerne og teste Azure Service strukturen programmer.  Administrerede og HTTP APIs til app management er også tilgængelige. Se [app supportlivscyklus](service-fabric-application-lifecycle.md) kan finde flere oplysninger.  

## <a name="prerequisites"></a>Forudsætninger
Inden du gå videre til opgaverne i artiklen, skal du sørge for at:

+ Bliv fortrolig med de Service-strukturen begreber, der er beskrevet i [Teknisk oversigt over Service struktur](service-fabric-technical-overview.md).
+ [Installere runtime, SDK, og værktøjer](service-fabric-get-started.md), som også installerer **ServiceFabric** PowerShell-modulet.
+ [Aktivere PowerShell-script udførelse af](service-fabric-get-started.md#enable-powershell-script-execution).
+ Starte en lokal klynge.  Start et nyt vindue i PowerShell som administrator og derefter køre scriptet klynge installationsprogrammet fra mappen SDK:`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Før du kører en PowerShell-kommandoer i denne artikel, først opretter forbindelse til den lokale Service-strukturen klynge ved hjælp af [**Opret forbindelse-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ Følgende opgaver kræver en v1 programpakke til at udrulle og en v2 programpakke til opgradering. Hent [ **WordCount** Northwind](http://aka.ms/servicefabricsamples) (findes i de første gang prøver). Opbygge og pakke programmet i Visual Studio (højreklik på **WordCount** i Solution Explorer og vælge **pakke**). Kopiere pakken v1 i `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` til `C:\Temp\WordCount`. Kopiér `C:\Temp\WordCount` til `C:\Temp\WordCountV2`, oprette v2 programpakke til opgradering. Åbn `C:\Temp\WordCountV2\ApplicationManifest.xml` i et tekstredigeringsprogram. Ændre egenskaben **ApplicationTypeVersion** fra "1.0.0" til "2.0.0" for at opdatere versionsnummeret app i elementet **ApplicationManifest** . Gem den ændrede ApplicationManifest.xml-fil.

## <a name="task-deploy-a-service-fabric-application"></a>Opgave: Installere en-strukturen-tjenesteprogram

Når du har oprettet og pakket programmet (eller hentet programpakken), kan du installere programmet i en lokal Service-strukturen klynge. Installation omfatter overførsel af programpakken, registrere programtypen og oprettelse af programmet forekomst. Bruge instruktionerne i dette afsnit til at installere et nyt program til en klynge.

### <a name="step-1-upload-the-application-package"></a>Trin 1: Uploade programpakken
Overførsel af programmet pakke til billede store placerer den på en placering, adgang til interne Service-strukturen komponenter.  Programmet pakken indeholder det er nødvendigt progammanifest, service manifester og kode, konfiguration og data pakker, der skal oprette programmet og forekomster af tjenesten.  Kommandoen [**Kopiér ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) uploader pakken. Eksempel:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Trin 2: Registrere programtypen
Registrering af programmet pakken gør programtype og version, der er defineret i programmanifestet tilgængelig til brug. Systemet læser pakken overført i trin 1, Kontroller pakken (svarende til kører [**Test ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) lokalt), behandle indholdet i pakken, og kopiere behandlede pakken til et internt systemplacering.  Køre [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) cmdlet:

```powershell
Register-ServiceFabricApplicationType WordCount
```
For at se alle de programtyper, der er registreret i klyngen skal du køre [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) cmdlet:

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Trin 3: Opret forekomsten af programmet
Kan være en forekomst af et program ved hjælp af en hvilken som helst program type version, som er blevet registreret ved hjælp af kommandoen [**Ny ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) . Navnet på hvert program er erklæret på implementere tid og skal starte med den **strukturen:** skema og være entydig for hver forekomst af programmet. Type programnavnet og programmet type version er erklæret i filen **ApplicationManifest.xml** af programpakken. Hvis standardtjenester er defineret i programmanifestet af typen target programmet, er dem, der er oprettet på nuværende tidspunkt.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Kommandoen [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) viser alle forekomster af tjenesteprogrammer, der blev oprettet, sammen med deres overordnede status. Kommandoen [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) viser alle de forekomster af tjenesten, der blev oprettet i en given programmet forekomst. Standardtjenester (hvis relevant) vises.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Opgave: Opgradere en-strukturen tjenesteprogram
Du kan opgradere en tidligere installerede Service-strukturen program med en opdateret programpakke. Denne opgave opgraderer WordCount programmet, der er implementeret i "opgave: installere en tjeneste-strukturen-program." Læs gennem [Service-strukturen programmet opgradere](service-fabric-application-upgrade.md) kan finde flere oplysninger.

Hvis du vil bevare ting enkel i dette eksempel, blev versionsnummeret programmet opdateret i den WordCountV2 programpakke, der er oprettet i forudsætningerne. Et scenarie med mere realistisk medfører opdaterer din tjeneste kode, konfiguration eller data filer og derefter genopbygge og emballagen programmet med tal, opdaterede version.  

### <a name="step-1-upload-the-updated-application-package"></a>Trin 1: Uploade den opdaterede programpakke
WordCount v1 programmet er klar til at blive opgraderet. Hvis du åbner et vindue i PowerShell som administrator og type [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), kan du se denne version 1.0.0 af typen WordCount program er installeret.  

Nu skal du kopiere den opdaterede programpakke til tjenesten strukturen billede store (hvor programpakker gemmes som Service struktur). Parameteren **ApplicationPackagePathInImageStore** oplyser Service-strukturen, hvor den kan finde programpakken. Følgende kommando kopierer programpakken til **WordCountV2** i store billede:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Trin 2: Registrere den opdaterede programtype
Næste trin er at registrere den nye version af programmet med tjenesten struktur, der kan udføres ved hjælp af [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) cmdlet:

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Trin 3: Starte opgraderingen
Forskellige opgradering parametre, timeout og sundhed kriterier kan anvendes på programmet opgraderinger. Læs gennem [programmet opgradere parametre](service-fabric-application-upgrade-parameters.md) og [opgraderingsprocessen](service-fabric-application-upgrade.md) dokumenter til at få mere at vide. Alle tjenester og forekomster skal være _sund_ efter opgraderingen.  Angiv **HealthCheckStableDuration** til 60 sekunder (så tjenesterne er sund i mindst 20 sekunder, før opgraderingen skrider frem til det næste opgradering domæne).  Også angive **UpgradeDomainTimeout** til 1200 sekunder og **UpgradeTimeout** til 3000 sekunder. Til sidst skal du angive **UpgradeFailureAction** til **rollback**, der anmoder om, at Service-strukturen annullerer programmet til den tidligere version Hvis der opstår fejl under opgraderingen.

Du kan nu starte opgraderingen program ved hjælp af [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) cmdlet:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Bemærk, at navnet på programmet er den samme som det tidligere udløst v1.0.0 programnavn (strukturen: / WordCount). Tjenesten strukturen bruger dette navn til at identificere, hvilket program få opgraderes. Hvis du angiver timeout skal være for kort, kan du støde på en meddelelse om fejl ved timeout, hvor der står problemet. Refererer til [fejlfinding i forbindelse med program opgraderinger](service-fabric-application-upgrade-troubleshooting.md)eller øge timeout.

### <a name="step-4-check-upgrade-progress"></a>Trin 4: Kontrollér opgradering fremskridt
Ved hjælp af [Tjenesten strukturen Explorer](service-fabric-visualizing-your-cluster.md)eller ved at bruge cmdlet'en [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) til, kan du overvåge programmet opgradering status:

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

I et par minutter, Cmdletten [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) viser, at alle opgradering domæner er blevet opgraderet (fuldført).

## <a name="task-test-a-service-fabric-application"></a>Opgave: Testprogram Service-strukturen

Hvis du vil skrive høj kvalitet tjenester, skal udviklere kan forårsage upålidelig infrastruktur fejl for at teste stabilitet deres tjenester. Tjenesten strukturen giver udviklere muligheden for at forårsage fejl handlinger og teste services tilstedeværelse af fejl ved hjælp af kaos og belastningsjustering test scenarier.  Læs gennem [mulighed for at teste oversigt](service-fabric-testability-overview.md) kan finde flere oplysninger.

### <a name="step-1-run-the-chaos-test-scenario"></a>Trin 1: Kør scenariet kaos test
Scenariet kaos test genererer fejl på tværs af hele Service-strukturen-klynge. Dette scenario komprimerer fejl Generelt set over måneder eller år til et par timer. Kombination af sekvensopdelt fejl med en høj fejl sats finder hjørne sager, der ville ellers mistes. I følgende eksempel kører scenariet kaos test i 60 minutter.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Trin 2: Køre scenariet failover test
Sekundære test scenarie destinationer en bestemt service partition i stedet for hele klynge, og andre tjenester går påvirket. Dette scenario gentager listen over en sekvens af simuleret fejl i validering af tjenesten, mens din forretningslogik kører. En fejl i tjenesten validering angiver et problem, der kræver yderligere undersøgelse. Failover test Bæltematerialets kun én fejl ad gangen, i modsætning til scenariet kaos test, som kan forårsage flere fejl. I følgende eksempel kører en failover test for 60 minutter mod strukturen: / WordCount/WordCountService tjeneste.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Opgave: Fjerne et Service-strukturen program
Du kan slette en forekomst af en udløst programmet, fjerne den klargjorte programtype fra klyngen og fjerne programmet pakke fra ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Trin 1: Fjern et programforekomst
Når en forekomst af programmet ikke længere er nødvendig, kan du fjerne den permanent ved hjælp af [**Fjern ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) cmdlet. Dette fjerner også automatisk alle tjenester, der hører til det program, permanent fjerne alle tjenestetilstand. Denne handling ikke kan fortrydes, og kan ikke gendannes application-tilstand.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Trin 2: Fjerne registreringen af programtypen
Når du ikke længere har brug for en bestemt version af en programtype, unregister ved hjælp af [**Fjerner registreringen af ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) cmdlet. Fjerne registreringen af ubrugte typer versioner lagerplads, der bruges af programpakke i store billede. En programtype kan registreres, så længe der er ingen programmer, der er en forekomst af mod det eller afventer programmet opgraderinger refererer til den.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Trin 3: Fjerne programpakken
Når programtypen er ikke-registrerede, kan programpakken fjernes fra store billede ved hjælp af [**Fjern ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) cmdlet.

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Næste trin
[Tjenesten strukturen programmet livscyklus](service-fabric-application-lifecycle.md)

[Installere et program](service-fabric-deploy-remove-applications.md)

[Opgradering af programmet](service-fabric-application-upgrade.md)

[Azure Service-strukturen cmdletter](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Azure Service-strukturen mulighed for at teste cmdletter](https://msdn.microsoft.com/library/azure/mt125844.aspx)
