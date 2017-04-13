<properties
   pageTitle="Tjenesten strukturen programmet installation | Microsoft Azure"
   description="Hvordan du installerer og fjerne programmer i tjenesten struktur"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Installere og fjerne ved hjælp af PowerShell-programmer

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Når en [programtype er blevet pakket][10], det er klar til installation på en Azure Service strukturen klynge. Installation omfatter følgende tre trin:

1. Overføre programpakken
2. Registrere programtypen
3. Oprette forekomsten af programmet

>[AZURE.NOTE] Hvis du bruger Visual Studio til implementering og fejlfinding af programmer på din lokale udvikling klynge, der alle de følgende trin håndteres automatisk via en PowerShell-script, der findes i mappen Scripts af programmet projektet. I denne artikel indeholder baggrund i hvad disse scripts klarer sig, så du kan udføre de samme handlinger uden for Visual Studio.

## <a name="upload-the-application-package"></a>Overføre programpakken

Overførsel af programpakken, placerer den på en placering, der er tilgængelig for interne Service-strukturen komponenter. Du kan bruge PowerShell til at udføre overførslen. Før du kører en PowerShell-kommandoer i denne artikel, start altid ved hjælp af [Opret forbindelse-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) til at oprette forbindelse til tjenesten strukturen klynge.

Antag, at du har en mappe med navnet *MyApplicationType* indeholder, der de nødvendige progammanifest, service manifester og kode/søgekonfigurationsdata pakker. Kommandoen [Kopiér ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) uploader pakken til klynge billede Store. Cmdletten **Get-ImageStoreConnectionStringFromClusterManifest** , som er en del af tjenesten strukturen SDK PowerShell-modulet, der bruges til at få vist billede store forbindelsesstreng.  For at importere modulet SDK skal du køre:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Du kan kopiere en programpakke fra *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* til *c:\temp\MyApplicationType* (Omdøb mappen "fejlfinding" til "MyApplicationType"). I følgende eksempel overførsler pakken:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Registrere programpakken

Registrering af programmet pakken gør programtype og version, der er defineret i programmanifestet tilgængelig til brug. Systemet læser pakken overført i det forrige trin skal du bekræfte pakken (svarer til at køre [Test ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) lokalt), behandle indholdet i pakken og kopiere behandlede pakken til et internt systemplacering.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

Kommandoen [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) returnerer kun, når systemet har kopieret pakken programmet på computeren. Hvor lang tid det tager, afhænger af indholdet af programpakken. Hvis det er nødvendigt, kan parameteren **- TimeoutSec** bruges til at levere en længere timeout. (Standardtimeout er 60 sekunder).

Kommandoen [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) viser alle korrekt registrerede programmer type versioner.

## <a name="create-the-application"></a>Oprette programmet

Du kan indsætte et program ved hjælp af en hvilken som helst program type version, som er blevet registreret via kommandoen [Ny ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx) . Navnet på hvert program skal starte med den *strukturen:* skema og være entydig for hver forekomst af programmet. En hvilken som helst standardtjenester, der er defineret i programmanifestet af typen target programmet oprettes på nuværende tidspunkt.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

Kommandoen [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) viser alle forekomster af tjenesteprogrammer, der blev oprettet, sammen med deres overordnede status.

Kommandoen [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) viser alle forekomster af tjenesten, der blev oprettet i en given programmet forekomst. Standardtjenester (hvis relevant) er angivet her.

Flere forekomster af tjenesteprogrammer kan oprettes for en given version af et registreret programtype. Hver forekomst af programmet kører i isolationsniveauet med sine egne arbejde directory og proces.

## <a name="remove-an-application"></a>Fjern et program

Når en forekomst af programmet ikke længere er nødvendig, kan du fjerne den permanent ved hjælp af kommandoen [Fjern ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Denne kommando fjerner automatisk alle tjenester, der hører til programmet samt permanent fjerne alle tjenestetilstand. Denne handling ikke kan fortrydes, og kan ikke gendannes application-tilstand.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Når en bestemt version af en programtype ikke længere er nødvendig, skal du fjerne registreringen af den ved hjælp af kommandoen [Fjerner registreringen af ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Fjerne registreringen af ubrugte typer versioner lagerplads, der bruges af programmet pakkeindholdet af den pågældende type på store billede. En programtype kan registreres, så længe ingen programmer er en forekomst af mod den og ikke afventer programmet opgraderinger refererer til den.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Kopiér ServiceFabricApplicationPackage beder om at få en ImageStoreConnectionString

Service-strukturen SDK miljøet skal allerede har de korrekte standardindstillinger for konfigurere. Men hvis det er nødvendigt, ImageStoreConnectionString for alle kommandoer skal svare til den værdi, der bruger tjenesten strukturen klynge. Du kan finde det i klynge manifestet hentet ved hjælp af kommandoen [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Næste trin

[Tjenesten strukturen programmet opgradering](service-fabric-application-upgrade.md)

[Tjenesten strukturen sundhed Introduktion](service-fabric-health-introduction.md)

[Diagnosticere og foretage fejlfinding af en tjeneste-strukturen tjeneste](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model et program i tjenesten struktur](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
