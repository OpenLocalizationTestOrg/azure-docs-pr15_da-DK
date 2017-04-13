<properties
   pageTitle="Tjenesten strukturen App Opgrader ved hjælp af PowerShell | Microsoft Azure"
   description="I denne artikel indeholder en gennemgang af installation af en tjeneste-strukturen programmet, ændre koden og distribution af en opgradering ved hjælp af PowerShell erfaringer."
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


# <a name="service-fabric-application-upgrade-using-powershell"></a>Tjenesten strukturen programmet Opgrader ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

Mest anvendte og anbefalede opgradering tilgang er overvåget rullende opgraderingen.  Azure Service-strukturen overvåger tilstanden af programmet ved at blive opgraderet baseret på et sæt af politikker for systemtilstand. Når en opdatering domæne (UD) er opgraderet, Service-strukturen evaluerer programmet tilstanden og enten skrider frem til det næste opdatering domæne eller mislykkes opgraderingen afhængigt af tilstandspolitikkerne, der.

En overvåget programmet opgradering kan udføres ved hjælp af den administrerede oprindelige API'er, PowerShell eller RESTEN. Flere oplysninger om at udføre en opgradering ved hjælp af Visual Studio, under [opgradering af dit program ved hjælp af Visual Studio](service-fabric-application-upgrade-tutorial.md).

Programmet administratoren kan konfigurere politikken sundhed evaluering, Service-strukturen bruger til at afgøre, om programmet er sund med tjenesten strukturen overvåges rullende opgraderinger. Desuden kan administratoren konfigurere handlingen, der skal udføres, når sundhed evalueringen mislykkes (for eksempel, at gøre en automatisk annullering.) Dette afsnit indeholder en gennemgang en overvåget opgradering af en af eksemplerne SDK, der bruger PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Trin 1: Byg og Implementer eksemplet visuelle objekter


Opbygge og udgive programmet ved at højreklikke på programmet projektet, **VisualObjectsApplication,** og vælge kommandoen **Udgiv** .  Du kan finde yderligere oplysninger finder [Service-strukturen programmet opgradere selvstudium](service-fabric-application-upgrade-tutorial.md).  Du kan også bruge PowerShell til at udrulle dit program.

> [AZURE.NOTE] Før en af kommandoerne Service-strukturen kan bruges i PowerShell, skal du først oprette forbindelse til klyngen ved hjælp af den `Connect-ServiceFabricCluster` cmdlet. På samme måde, forudsættes det, at klyngen har allerede konfigureret på din lokale computer. Se artiklen om [oprettelse af din Service-strukturen udviklingsmiljø](service-fabric-get-started.md).

Når du opbygger projektet i Visual Studio, kan du bruge PowerShell-kommandoen **Kopiér ServiceFabricApplicationPackage** til at kopiere programpakken til ImageStore. Næste trin er at registrere programmet til ved hjælp af **Register-ServiceFabricApplicationPackage** cmdlet til kørsel af tjenesten struktur. Det sidste trin er at starte en forekomst af programmet ved hjælp af **Ny ServiceFabricApplication** cmdlet.  Disse tre trin, der svarer til at bruge menupunktet **Implementer** i Visual Studio.

Du kan nu bruge [Tjenesten strukturen Stifinder til at få vist klyngen og programmet på computeren](service-fabric-visualizing-your-cluster.md). Programmet, har en webtjeneste, der kan navigere til i Internet Explorer ved at skrive [http://localhost: 8081/visualobjects](http://localhost:8081/visualobjects) i adresselinjen.  Du bør se nogle flydende visuelle objekter bevæge sig rundt i skærmbilledet.  Desuden kan du bruge **Get-ServiceFabricApplication** til at kontrollere status for programmet på computeren.

## <a name="step-2-update-the-visual-objects-sample"></a>Trin 2: Opdatere eksemplet visuelle objekter

Du oplever måske, at med den version, der blev installeret i trin 1, de visuelle objekter Undlad at rotere. Lad os opgradere dette program på ét sted, hvor de visuelle objekter også rotere.

Vælg VisualObjects.ActorService projektet i VisualObjects løsningen, og Åbn filen StatefulVisualObjectActor.cs. I den pågældende fil, Naviger til metoden `MoveObject`, skrive en kommentar ud `this.State.Move()`, og fjern kommentar fra `this.State.Move(true)`. Denne ændring roterer objekterne, når tjenesten er opgraderet.

Vi skal også opdatere filen *ServiceManifest.xml* (under PackageRoot) af projektet **VisualObjects.ActorService**. Opdatere *CodePackage* og service-version 2.0, og de tilhørende linjer i filen *ServiceManifest.xml* .
Når du højreklikker på løsningen til at foretage manifestfilen ændringer, kan du bruge indstillingen Visual Studio *Redigere manifestet filer* .


Når de ændringer, Manifestet skal se ud som følgende (fremhævede dele viser ændringerne foretaget):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu opdateres filen *ApplicationManifest.xml* (findes under **VisualObjects** projektet under **VisualObjects** løsningen) version 2.0 af **VisualObjects.ActorService** projektet. Desuden opdateres versionen af programmet til 2.0.0.0 fra 1.0.0.0. *ApplicationManifest.xml* skal se ud som følgende kodestykke:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


Nu oprette projektet ved at vælge lige **ActorService** projektet, og derefter højreklikke og vælge indstillingen **opbygge** i Visual Studio. Hvis du vælger **genopbygge alle**, skal du opdatere versionerne for alle projekter, da koden ville være ændret. Næste, Lad os pakke det opdaterede program ved at højreklikke på ***VisualObjectsApplication***, vælge menuen Service-strukturen og vælge **pakke**. Denne handling opretter en programpakke, der kan være installeret.  Opdaterede programmet er klar til at være installeret.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Trin 3: Beslutte på tjenestetilstand politikker og opgradere parametre

Lære at kende af [programmet opgradere parametre](service-fabric-application-upgrade-parameters.md) og de [opgraderingsprocessen](service-fabric-application-upgrade.md) at få en god forståelse af de forskellige opgradering parametre, timeout og sundhedskriterium, der er anvendt. For denne gennemgang er tjenestetilstand evaluering kriterium angivet til standard (og anbefalede) værdier, hvilket betyder, at alle tjenester og forekomster skal være _sund_ efter opgraderingen.  

Lad os øge dog *HealthCheckStableDuration* til 60 sekunder, (så tjenesterne er sund i mindst 20 sekunder, før opgraderingen skrider frem til det næste opdatering domæne).  Lad os også indstille *UpgradeDomainTimeout* skal være 1200 sekunder og *UpgradeTimeout* skal være 3000 sekunder.

Til sidst, Lad os også angive *UpgradeFailureAction* til rollback. Denne indstilling kræver Service-strukturen gå tilbage programmet til den tidligere version, hvis der opstår problemer under opgraderingen. Når du starter opgraderingen (i trin 4), angives følgende parametre således:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Trin 4: Forberede programmet til opgradering

Programmet er nu indbygget og klar til at blive opgraderet. Hvis du åbner et vindue i PowerShell som administrator, og skriv **Get-ServiceFabricApplication**, skal den fortæller dig, at den er programtype 1.0.0.0 af **VisualObjects** , der er implementeret.  

Pakken programmet er gemt under den følgende relative sti, hvor du ikke-komprimeret format Service-strukturen SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Du skal finde en mappe med "Pakke" i mappen, hvor programpakken er gemt. Kontrollere tidsstempler for at sikre, at det er det seneste build (du skal muligvis ændre stier korrekt).

Nu Lad os kopiere den opdaterede programpakke til tjenesten strukturen ImageStore (hvor programpakker gemmes som Service struktur). Parameteren *ApplicationPackagePathInImageStore* oplyser Service-strukturen, hvor den kan finde programpakken. Vi har indsat det opdaterede program i "VisualObjects\_V2" med følgende kommando (du skal muligvis ændre stier igen korrekt).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Næste trin er at registrere dette program med tjenesten struktur, der kan udføres ved hjælp af følgende kommando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Hvis en foregående kommando ikke lykkes, er det sandsynligvis, at du har brug for en Genopbyg for alle tjenester. Som nævnt i trin 2, kan du muligvis opdatere din webtjeneste version.

## <a name="step-5-start-the-application-upgrade"></a>Trin 5: Starte programmet opgraderingen

Nu skal er vi alle indstillet til at starte opgraderingen program ved hjælp af følgende kommando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Navnet på programmet er den samme som det er beskrevet i filen *ApplicationManifest.xml* . Tjenesten strukturen bruger dette navn til at identificere, hvilket program få opgraderes. Hvis du angiver timeout skal være for kort, kan du støde på en meddelelse, hvor der står problemet. Fejlfinding i forbindelse med afsnittet eller øge timeout.

Du kan nu som programmet opgradering overskuddet, overvåge den ved hjælp af tjenesten strukturen Explorer eller ved hjælp af følgende PowerShell-kommando: **Get-ServiceFabricApplicationUpgrade-strukturen: / VisualObjects**.

I et par minutter, status, du fik ved hjælp af kommandoen foregående PowerShell, står, at alle opdatering domæner er blevet opgraderet (fuldført). Og du skal finde, at de visuelle objekter i browservinduet har startet rotere!

Du kan prøve opgradering fra version 2 til version 3 eller fra version 2 til version 1 som en opgave. Flytte fra version 2 til version 1 betragtes også som en opgradering. Lege med timeout og sundhed politikker til at foretage selv fortrolig med dem. Når du installerer på en Azure klynge, skal parametrene, der være angivet korrekt. Det er godt at angive timeout konservativt.


## <a name="next-steps"></a>Næste trin

[Opgradering af dit program ved hjælp af Visual Studio](service-fabric-application-upgrade-tutorial.md) vejleder dig gennem programmet opgraderes ved hjælp af Visual Studio.

Styre, hvordan dit program opgraderer ved hjælp af [upgrade parametre](service-fabric-application-upgrade-parameters.md).

Gøre dit program opgraderinger kompatibel ved at lære, hvordan du bruger [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lær at bruge avancerede funktioner under opgradering af dit program ved at referere til [Avancerede emner](service-fabric-application-upgrade-advanced.md).

Rette almindelige fejl i programmet opgraderinger ved at referere til trinnene i [fejlfinding programmet opgraderinger](service-fabric-application-upgrade-troubleshooting.md).
