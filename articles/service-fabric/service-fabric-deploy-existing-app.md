<properties
   pageTitle="Anvende en eksisterende eksekverbar fil til Azure Service strukturen | Microsoft Azure"
   description="Gennemgang om, hvordan du pakke et eksisterende program som gæst eksekverbare, så den kan installeres på en tjeneste-strukturen klynge"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Installere gæst eksekverbare til tjenesten struktur

Du kan køre alle typer-program, som node.js, Java eller oprindelige programmer i Azure Service struktur. Tjenesten strukturen refererer til disse typer programmer som gæst eksekverbare filer.
Gæst eksekverbare behandlet af tjenesten strukturen som uden status tjenester. Som et resultat, er de placeret på noderne i en klynge, baseret på tilgængelighed og anden metrik. I denne artikel beskrives, hvordan at pakke og installere gæst eksekverbare til en tjeneste-strukturen klynge, ved hjælp af Visual Studio eller et værktøj til kommandolinjen.

I denne artikel dækker vi trinnene til at pakke gæst eksekverbare og installerer det til tjenesten struktur.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Fordele ved at køre gæst eksekverbare i tjenesten struktur

Der er flere fordele, der følger med kører gæst eksekverbare i en tjeneste-strukturen klynge:

- Høj tilgængelighed. Programmer, der kører i tjenesten strukturen gøres meget tilgængelige. Tjenesten strukturen sikrer, at der kører forekomster af et program.
- Sundhedsovervågning. Tjenesten strukturen sundhedsovervågning registrerer, hvis et program, der kører og giver diagnosticeringsoplysninger, hvis der er en fejl.   
- Livscyklus programadministration. Ud over nem opgraderinger uden nedetid, indeholder Service-strukturen automatisk annullering til den tidligere version, hvis der er en ugyldig tilstand begivenhed, der rapporteres under en opgradering.    
- Tæthed. Du kan køre flere programmer i en klynge, som fjerner behovet for hvert program for at køre på sin egen hardware.


## <a name="overview-of-application-and-service-manifest-files"></a>Oversigt over programmet og service manifestfiler

Som en del af implementerer gæst eksekverbare, det er nyttigt at forstå emballagen Service-strukturen og implementeringsmodel som beskrevet [programmet model](service-fabric-application-model.md). Tjenesten strukturen emballagen modellen er afhængig af to XML-filer: de program, der service manifester. Skemadefinitionen for ApplicationManifest.xml og ServiceManifest.xml filerne er installeret med Service-strukturen SDK i *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Programmanifestet** Programmanifestet bruges til at beskrive programmet. Det viser de tjenester, der sammensætter den og andre parametre, der bruges til at definere, hvordan en eller flere tjenester skal installeres, som antallet forekomster.

  I Service-strukturen er et program en måleenhed installation og opgradering. Et program kan opgraderes som en enkelt enhed, hvor eventuelle fejl og mulige Rollback administreres. Tjenesten strukturen garanterer, at opgraderingen er enten lykkes, eller Hvis opgraderingen mislykkes, efterlader ikke programmet i en ukendt/ustabil tilstand.

* **Tjenesten manifestet** Tjenestemanifestet beskrives komponenterne i en tjeneste. Den indeholder data, som navn og type tjeneste, og den kode, konfiguration og Data. Tjenestemanifestet indeholder også nogle yderligere parametre, der kan bruges til at konfigurere tjenesten, når den er installeret.


## <a name="application-package-file-structure"></a>Programmet pakke filstruktur
Hvis du vil installere et program til tjenesten struktur, skal programmet følge en foruddefineret mappestrukturen. I følgende eksempel af strukturen.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

ApplicationPackageRoot indeholder filen ApplicationManifest.xml, der definerer programmet. En undermappe til hver tjeneste, der er inkluderet i programmet bruges til at indeholde alle de elementer, der kræver tjenesten – ServiceManifest.xml og typisk følgende tre mapper:

- *Kode*. Denne mappe indeholder koden.
- *Config*. Denne mappe indeholder en Settings.xml-fil (og andre filer, hvis det er nødvendigt), tjenesten kan få adgang til på kørselstidspunktet til at hente bestemte konfigurationsindstillinger.
- *Data*. Dette er en ekstra mappe til at gemme flere lokale data, som kan være nødvendigt tjenesten. Bemærk: Data skal bruges til at gemme kun kortvarige data. Tjenesten strukturen ikke Kopiér/replikeres ændringer til datamappen Hvis tjenesten skal flyttes – for eksempel under failover.

Bemærk: Du ikke behøver at oprette den `config` og `data` kataloger, hvis du ikke har brug for dem.

## <a name="packaging-an-existing-executable"></a>Pakke en eksisterende eksekverbar fil

Når emballagen en gæst eksekverbar fil, kan du vælge enten at bruge en skabelon til Visual Studio project eller [oprette programpakken manuelt](#manually). Brug af Visual Studio, er programmet pakke strukturen og manifestfilerne oprettet af guiden project for dig.

>[AZURE.NOTE] Den nemmeste måde at pakke en eksisterende Windows eksekverbare til en tjeneste er at bruge Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Brug af Visual Studio til at pakke en eksisterende eksekverbar fil

Visual Studio indeholder en tjeneste-strukturen serviceskabelon for at hjælpe dig med at installere gæst eksekverbare til en tjeneste-strukturen klynge.

Gå gennem følgende trin til at fuldføre udgivelse:

1. Vælg Filer -> nyt projekt og oprette et tjenesteprogram struktur.
2. Vælg gæst eksekverbar fil som skabelonen med tjenesten.
3. Klik på Gennemse for at markere mappen med din eksekverbar fil, og Udfyld resten af parametre til at oprette tjenesten.
    - *Kode pakken funktionsmåde* kan angive for at kopiere indholdet af mappen til Visual Studio-projektet, hvilket er nyttigt, hvis den eksekverbare fil ikke ændres. Hvis du forventer den eksekverbare fil til at ændre og vil muligheden for at vælge Ny builds dynamisk, kan du vælge at oprette et link til mappen i stedet. Bemærk, at du kan bruge sammenkædede mapper, når du opretter programmet projektet i Visual Studio. Denne links til kildeplacering fra i projektet, gør det muligt for dig at opdatere den eksekverbare gæst i destinationen kilde har disse opdateringer, bliver en del af programpakke på build.
    - *Program* - Vælg den eksekverbare fil, der skal køres for at starte tjenesten.
    - *Argumenter* – Angiv de argumenter, der skal overføres til den eksekverbare fil. Det kan være en liste over parametre med argumenter.
    - *WorkingFolder* - angiver arbejdsmappen for den proces, der skal startes. Du kan angive tre værdier:
        - `CodeBase`Angiver, at arbejdsmappen vil være indstillet til mappen kode i programpakken (`Code` mappe i den filstruktur vises før).
        - `CodePackage`Angiver, at arbejdsmappen der skal angives i roden af programpakken (`GuestService1Pkg` i filen strukturen vises før).
        - `Work`Angiver, at filerne er placeret i en undermappe med navnet arbejde
4. Navngiv din tjeneste, og klik på OK.
5. Hvis din tjeneste skal et slutpunkt for kommunikation, kan du nu føje protokol, Port og Type til filen ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Du kan nu Brug funktionen pakke og udgive handling mod dine lokale klynge ved fejlfinding løsningen i Visual Studio. Når du er klar kan du udgive-programmet til en ekstern klynge eller indtjekning løsningen til versionsstyring.
7. Gå til slutningen af denne artikel for at se, hvordan du får vist gæst eksekverbare tjenesten kører i tjenesten strukturen Explorer.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Manuelt emballagen og implementere en eksisterende eksekverbar fil
Processen for at manuelt pakke en gæst eksekverbar fil er baseret på følgende trin:

1. Oprette mappestrukturen pakke.
2. Føje programmets kode og konfiguration af filer.
3. Rediger den tjenestemanifestfilen.
4. Redigere manifestfilen til programmet.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Oprette mappestrukturen pakke
Du kan starte med at oprette mappestrukturen, som beskrevet tidligere.

### <a name="add-the-applications-code-and-configuration-files"></a>Føje programmets kode og konfiguration af filer
Når du har oprettet mappestrukturen, kan du tilføje programmets kode og af konfigurationsfiler under mapperne kode og config. Du kan også oprette yderligere mapper eller undermapper under mapperne kode eller config.

Tjenesten strukturen understøtter en xcopy af indholdet af programmets rodmappe, så der er ingen foruddefinerede struktur bruge andet end at oprette to øverste kataloger, kode og indstillinger. (Du kan vælge forskellige navne, hvis du vil have. Få mere at vide er i næste afsnit.)

>[AZURE.NOTE] Sørg for, at du medtager alle de filer/afhængigheder, som programmet skal. Tjenesten strukturen kopierer indholdet af programpakke på alle noder i den klynge, hvor den programtjenester skal være installeret. Pakken skal indeholde den kode, der skal køre programmet. Vi anbefaler ikke at antage, at der allerede er installeret på afhængigheder.

### <a name="edit-the-service-manifest-file"></a>Redigere den tjenestemanifestfilen
Det næste trin er at redigere tjenestemanifestfilen for at medtage følgende oplysninger:

- Navnet på typen. Dette er et ID-tjenesten strukturen bruges til at identificere en tjeneste.
- Kommandoen til at bruge til at starte programmet (ExeHost).
- En hvilken som helst script, der skal køres for at angive op/konfigurere programmet (SetupEntrypoint).

Følgende er et eksempel på en `ServiceManifest.xml` fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Lad os se på de forskellige dele af den fil, du vil opdatere:

#### <a name="updating-the-servicetypes"></a>Opdatere ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Du kan vælge en hvilken som helst navn, du vil for `ServiceTypeName`. Værdien, der bruges i de `ApplicationManifest.xml` fil for at identificere tjenesten.
- Du skal angive `UseImplicitHost="true"`. Denne attribut fortæller Service-strukturen, tjenesten er baseret på en selvstændig app, så alle Service-strukturen behøver at gøre, er at starte det som en proces og overvåge dens tilstand.

#### <a name="updating-the-codepackage"></a>Opdatere CodePackage
Elementet CodePackage angiver placeringen (og version) af tjenestens kode.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Den `Name` element bruges til at angive navnet på mappen i programpakken, der indeholder tjenestens kode. `CodePackage`har også den `version` attributter. Dette kan bruges til at angive versionen af koden – og også potentielt kan bruges til at opgradere tjenestens kode ved hjælp af tjenesten strukturen programmet livscyklus management infrastruktur.
#### <a name="optional-updating-the-setupentrypoint"></a>Valgfrit: Opdatering af SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Elementet SetupEntrypoint bruges til at angive en eksekverbar fil eller batch-fil, der skal udføres, før den tjenestekode startes. Det er et valgfrit trin, så det ikke nødvendigt at indgå, hvis der ikke er nogen initialisering/installation, der kræves. SetupEntryPoint udføres, hver gang tjenesten genstartes.

Der er kun én SetupEntrypoint, så installationen/config scripts skal grupperes i en enkelt batchfil, hvis programmets konfiguration/config kræver flere scripts. SetupEntrypoint kan udføre alle typer filer – eksekverbare filer, batchfiler og PowerShell-cmdlet'er. Du kan finde flere detaljer, [Konfigurere SetupEntryPoint](service-fabric-application-runas-security.md).

I det foregående eksempel på SetupEntrypoint kører en batchfil kaldet `LaunchConfig.cmd` , der er placeret i den `scripts` undermappe til mappen kode (hvis elementet WorkingFolder er indstillet til CodeBase).

#### <a name="updating-the-entrypoint"></a>Opdatere indgangspunktet

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Den `Entrypoint` elementet i manifestfilen service bruges til at angive, hvordan du kan starte tjenesten. Den `ExeHost` element angiver den eksekverbare fil (og argumenter), der skal bruges til at starte tjenesten.

- `Program`Angiver navnet på den eksekverbare fil, der skal udføres for at starte tjenesten.
- `Arguments`Angiver de argumenter, der skal overføres til den eksekverbare fil. Det kan være en liste over parametre med argumenter.
- `WorkingFolder`Angiver arbejdsmappen for den proces, der skal startes. Du kan angive tre værdier:
    - `CodeBase`Angiver, at arbejdsmappen vil være indstillet til mappen kode i programpakken (`Code` mappe i den foregående filstruktur).
    - `CodePackage`Angiver, at arbejdsmappen der skal angives i roden af programpakken (`GuestService1Pkg` i den foregående filstruktur).
  - `Work`Angiver, at filerne er placeret i en undermappe med navnet arbejde

WorkingFolder er praktisk til at angive den korrekte arbejdsmappe, så relative stier kan anvendes af programmet eller initialisering scripts.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Opdatere slutpunkterne og registrering af med navngivning af tjenesten til kommunikation

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
I det foregående eksempel på `Endpoint` element angiver de slutpunkter, programmet kan lytter på. I dette eksempel lytter programmet Node.js på http på port 3000.

Desuden kan du bede Service-strukturen til at publicere dette slutpunkt til navngivning af tjenesten, så andre tjenester kan opdage slutpunktsadressen til denne tjeneste. Dette gør det muligt at kunne kommunikere mellem tjenester, der er gæst eksekverbare filer.
Slutpunktsadressen, publicerede er i formularen `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`og `PathSuffix` er valgfrit attributter. `IPAddressOrFQDN`er den IP-adresse eller det fulde domænenavn for noden denne eksekverbare fil bliver, som anbringes på og beregnes for dig.

I følgende eksempel når tjenesten er installeret, i tjenesten strukturen Explorer du vist et slutpunkt ligner `http://10.1.4.92:3000/myapp/` publiceret for forekomsten af eller, hvis dette er en lokal computer, du kan se `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Du kan bruge disse adresser med [omvendt proxy](service-fabric-reverseproxy.md) til at kommunikere mellem tjenester.

### <a name="edit-the-application-manifest-file"></a>Redigere manifestfilen til programmet

Når du har konfigureret den `Servicemanifest.xml` fil, du vil foretage ændringer til det `ApplicationManifest.xml` fil for at sikre, at de korrekte tjenestetype og navn anvendes.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

I den `ServiceManifestImport` element, du kan angive en eller flere tjenester, du vil medtage i appen. Tjenester der refereres til med `ServiceManifestName`, som angiver navnet på mappen, hvor den `ServiceManifest.xml` filen er placeret.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurere logføring
Til gæst eksekverbare filer er det nyttigt at kunne se console logfiler til at finde ud af, hvis programmet og konfiguration scriptene viser eventuelle fejl.
Console omdirigering kan konfigureres i den `ServiceManifest.xml` fil via den `ConsoleRedirection` element.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`kan bruges til at omdirigere console output (både stdout og stderr) til en arbejdsmappe, så de kan bruges til at bekræfte, at der er ingen fejl under installation eller udførelse af programmet i tjenesten strukturen klynge.

    * `FileRetentionCount`bestemmer, hvor mange filer gemmes i den aktuelle mappe. En værdi af 5, betyder f.eks, at logfilerne til de forrige fem udførelser er gemt i den aktuelle mappe.
    * `FileMaxSizeInKb`Angiver den maksimale størrelse af logfiler.

Logfiler gemmes i en af tjenestens arbejde kataloger. Hvis du vil finde ud af, hvor filerne er placeret, skal du bruge Service-strukturen Stifinder til at bestemme, hvilke node, tjenesten kører, og hvilke arbejdsmappe bruges. Denne proces dækkes senere i denne artikel.

## <a name="deployment"></a>Installation
Det sidste trin er at installere programmet. Følgende PowerShell-script viser, hvordan du installerer programmet til den lokale udvikling klynge og starte en ny tjeneste strukturen tjeneste.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
En tjeneste-strukturen tjeneste kan installeres i forskellige "konfigurationer." For eksempel det kan anvendes som et eller flere forekomster, eller den kan installeres på en sådan måde, at der er én forekomst af tjenesten på hver node i tjenesten strukturen klynge.

Den `InstanceCount` -parameter for den `New-ServiceFabricService` cmdlet bruges til at angive, hvor mange forekomster af tjenesten skal startes i tjenesten strukturen klynge. Du kan angive den `InstanceCount` værdi, afhængigt af typen program, du installerer. De to mest almindelige scenarier er:

* `InstanceCount = "1"`. I dette tilfælde er kun én forekomst af tjenesten implementeret i klyngen. Tjenesten strukturen scheduler bestemmer, hvilke node, der skal være installeret på tjenesten.

* `InstanceCount ="-1"`. I dette tilfælde er én forekomst af tjenesten installeret på hver node i tjenesten strukturen klynge. Resultatet er et én (og kun én) forekomsten af tjenesten for hver enkelt node i klyngen.

Dette er en praktisk konfiguration for front end-programmer (for eksempel en RESTEN slutpunkt), fordi klientprogrammer have "oprette forbindelse" for knuderne i klynge for at bruge slutpunktet. Denne konfiguration kan også bruges, når eksempelvis alle noder af tjenesten strukturen klynge er tilsluttet en belastningsjustering, så klienttrafik kan distribueres på tværs af tjenesten, der kører på alle noder i klyngen.

## <a name="check-your-running-application"></a>Kontrollere din aktive program

Identificere noden, hvor tjenesten kører i tjenesten strukturen Stifinder. I dette eksempel køres der på Node1:

![Node, hvor tjenesten kører](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Hvis du gå til noden og gå til programmet, kan du se de væsentlige nodeoplysninger, herunder dets placering på disken.

![Placering på disk](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Hvis du gå til mappen ved hjælp af Server Explorer, kan du finde den aktuelle mappe og tjenestens logmappe som følgende billede viser.

![Placering af logfiler](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Næste trin
Du har lært Sådan pakke en gæst eksekverbar fil og installerer det til tjenesten strukturen i denne artikel. Som et næste trin, kan du se yderligere indhold i dette emne.

- [Stikprøve for emballagen og implementere gæst eksekverbare på GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), herunder et link til den foreløbige version af værktøjet emballagen
- [Installere flere gæst eksekverbare filer](service-fabric-deploy-multiple-apps.md)
- [Oprette din første Service-strukturen-program ved hjælp af Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
