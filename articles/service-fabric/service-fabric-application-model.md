<properties
   pageTitle="Tjenesten strukturen programmet model | Microsoft Azure"
   description="Sådan-modeller og beskriver programmer og tjenester i tjenesten struktur."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Model et program i tjenesten struktur

I denne artikel indeholder en oversigt over Azure Service strukturen programmet modellen. Det også beskrives, hvordan du definerer et program og tjenesten via manifestfiler og få programmet, pakket og klar til installation.

## <a name="understand-the-application-model"></a>Forstå programmet datamodellen

Et program er en samling af enkelte tjenester, som udfører en bestemt funktion eller funktioner. En tjeneste udfører en fuldført og enkeltstående funktion (den kan starte og køre uafhængigt af andre tjenester) og består af kode, konfiguration og data. Kode består af de eksekverbare binære filer for hver tjeneste, konfiguration består af Tjenesteindstillinger, der kan indlæses på kørselstidspunktet, og data består af vilkårlig statiske data at blive brugt i tjenesten. Hver komponent i denne model hierarkiske programmet kan være versionsnummer og opgraderede uafhængigt af hinanden.

![Tjenesten strukturen programmet modellen][appmodel-diagram]


En programtype er en inddeling af et program og består af en samlet typer service. En tjenestetype er en inddeling af en tjeneste. Feltet kategorisering kan have forskellige indstillinger og konfigurationer, men den grundlæggende funktionalitet forbliver uændret. Forekomster af en tjeneste er de anden tjeneste konfiguration variationer af den samme type.  

Klasser (eller "typer") programmer og tjenester er beskrevet via XML-filer (programmet manifester og service manifester), er de skabeloner, som programmer kan være en forekomst fra den klynge billede store. Skemadefinitionen for filen ServiceManifest.xml og ApplicationManifest.xml er installeret med Service-strukturen SDK og værktøjer til *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

Koden for andet programforekomster kører som særskilte processer, selvom hostet af den samme Service-strukturen node. Desuden kan livscyklus for hver forekomst af programmet administreres (dvs. opgraderede) uafhængigt af hinanden. I det følgende diagram viser, hvordan programmet typer består af tjenestetyper, der også består af kode, konfiguration og pakker. For at forenkle diagrammet, kun den kode / / søgekonfigurationsdata pakker til `ServiceType4` , vises, selvom hver tjenestetype ville medtage nogle eller alle de pakke typer.

![Tjenesten strukturen programmet typer og servicetyper][cluster-imagestore-apptypes]

To forskellige manifestfiler bruges til at beskrive programmer og tjenester: tjenestemanifestet og progammanifest. Disse er beskrevet i detaljer i de efterfølgende afsnit.

Der kan være en eller flere forekomster af en tjenestetype aktiv i klyngen. For eksempel opnå forekomster af med høj sikkerhed tjenesten eller replikaer, høj pålidelighed ved replikering tilstand mellem replikaer, der er placeret på forskellige noder i klyngen. Denne replikering indeholder grundlæggende redundans for tjenesten skal være tilgængeligt, selv hvis en node i en klynge mislykkes. En [partitioneret service](service-fabric-concepts-partitioning.md) yderligere dividerer dens tilstand (og access mønstre til tilstanden) på tværs af noder i klyngen.

I det følgende diagram viser forholdet mellem programmer og forekomster af tjenesten, partitioner og replikaer.

![Partitioner og replikaer inden for en tjeneste][cluster-application-instances]


>[AZURE.TIP] Du kan se layoutet af programmer i en klynge ved hjælp af værktøjet Service-strukturen Explorer tilgængelig på http://&lt;yourclusteraddress&gt;: 19080/Explorer. Yderligere oplysninger finder du [visualisere din klynge med tjenesten strukturen Stifinder](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Beskriv en tjeneste

Tjenestemanifestet definerer med en erklæring tjenestetype og version. Det angiver service metadata som tjenestetype, sundhed egenskaber, justering af belastning målepunkter, service binære filer og af konfigurationsfiler.  Sætte en anden måde, skal beskriver den de kode, konfiguration og data-pakker, der udgør en servicepakke at understøtte en eller flere tjenestetyper. Her er et simpelt eksempel tjenestemanifestet:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

**Version** attributter er ustrukturerede strenge og parset ikke af systemet. Dette er brugt til at version hver komponent til opgraderinger.

**ServiceTypes** erklærer, hvilke tjenester der understøttes af **CodePackages** i denne manifestfil. Når en tjeneste er en forekomst af mod en af disse service, aktiveres alle kode-pakker, der er defineret i denne manifestfil ved at køre deres indgangspunkter. De resulterende processer forventes til at registrere typerne tjeneste på kørselstidspunktet. Bemærk, at tjenestetyper er erklæret på niveauet for manifestets og ikke kode pakken niveauet for. Så når der er flere kode-pakker, aktiveres de alle, når der søges efter en af de angivne servicetyper.

**SetupEntryPoint** er en privilegerede indgangspunkt, der kører med de samme legitimationsoplysninger som Service-strukturen *(typisk kontoen LocalSystem)* før alle andre indgangspunkt. Den eksekverbare fil, der er angivet af **indgangspunkt** er typisk Tjenesteværten længerevarende. Tilstedeværelsen af en separat opsætning indgangspunkt undgår at skulle holde værten service med omfattende rettigheder for længere perioder. Den eksekverbare fil, der er angivet af **indgangspunkt** køres, når **SetupEntryPoint** afsluttet korrekt. Den resulterende proces overvåges og genstartet (starten igen med **SetupEntryPoint**), hvis det nogensinde ophører eller går ned.

**DataPackage** erklærer en mappe ved navn via attributten **navn** , der indeholder vilkårlig statiske data at blive brugt i processen på kørselstidspunktet.

**ConfigPackage** erklærer en mappe ved navn ved attributten **Name** , der indeholder en *Settings.xml* fil. Denne fil indeholder sektioner af brugerdefinerede, nøgle-værdi-par indstillinger, der kan læse processen tilbage på kørselstidspunktet. Under en opgradering, hvis der kun **ConfigPackage** **version** er ændret, genstartes derefter den proces, der kører ikke. I stedet et tilbagekald giver besked om den proces, der har ændret konfigurationsindstillinger, så de kan genindlæses dynamisk. Her er et eksempel *Settings.xml* fil:

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] En tjenestemanifestet kan indeholde flere kode, konfiguration og datapakker. Hver af dem, der kan være versionsnummer uafhængigt af hinanden.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Beskriv et program


Programmanifestet beskrives med en erklæring programtype og version. Det angiver service sammensætning metadata som stabil navne, partitionering farveskema, forekomst Tæl/gentagelse faktor, sikkerhed/isolationsniveauet politik, placeringen begrænsninger, konfiguration tilsidesætter og enkelte servicetyper. Justering af belastning domæner, der omgiver programmet er også beskrevet.

Dermed, et progammanifest beskrives elementer på niveauet for programmet på computeren og refererer til en eller flere service-manifester for at skrive en programtype. Her er et simpelt eksempel progammanifest:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Sådan service manifester **Version** attributter ustrukturerede strenge og parses ikke af systemet. Dette er også bruges til at version hver komponent til opgraderinger.

**ServiceManifestImport** indeholder referencer til tjenesten manifester, der sammensætter denne programtype. Importerede service manifester bestemme, hvilke tjenestetyper af er gyldige i denne programtype.

**DefaultServices** erklærer forekomster af tjenesten, der oprettes automatisk, når et program er en forekomst af mod denne programtype. Standard services er kun en nemmere og opfører sig som normal tjenester i hver forbindelse, når de er blevet oprettet. De er opgraderet sammen med andre tjenester i programmet forekomst og kan fjernes også.

> [AZURE.NOTE] Et progammanifest kan indeholde flere service manifest importerer og standardtjenester. Hver tjeneste manifestimport kan være versionsnummer uafhængigt af hinanden.

For at lære at vedligeholde andet program og parametre for individuelle-miljøer, skal du se [administrere programmets parametre for flere miljøer](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Pakke et program

### <a name="package-layout"></a>Pakke layout

Programmanifestet, service manifest(s) og andre nødvendige pakkefiler skal være organiseret i et bestemt layout til installation i en tjeneste-strukturen klynge. Eksempel-manifester i denne artikel er nødt til at være organiseret i følgende mappestrukturen:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Mapperne er navngivet så det svarer til **navnet** attributterne for hvert tilsvarende element. Hvis tjenestemanifestet indeholdt to kode pakker med navnene **MyCodeA** og **MyCodeB**, derefter to mapper med de samme navne for eksempel indeholde de nødvendige binære filer for hver kode pakke.

### <a name="use-setupentrypoint"></a>Brug SetupEntryPoint

Typiske scenarier for brug af **SetupEntryPoint** er, når du har brug at køre en eksekverbar fil inden tjenesten starter, eller du har brug at udføre en handling med administratorrettigheder. Eksempel:

- Konfigurere og initialisering miljøvariabler, som den eksekverbare fil service skal. Dette er ikke begrænset til kun eksekverbare skrevet via tjenesten strukturen programming modellerne. For eksempel skal npm.exe nogle miljøvariabler, der er konfigureret til at implementere et node.js program.

- Konfigurere adgangskontrol ved at installere sikkerhedscertifikater.

### <a name="build-a-package-by-using-visual-studio"></a>Oprette en pakke med Visual Studio

Hvis du bruger Visual Studio 2015 til at oprette dit program, kan du bruge kommandoen pakke til automatisk for at oprette en pakke, der svarer til det layout, der er beskrevet ovenfor.

Hvis du vil oprette en pakke, skal du højreklikke på programmet projektet i Solution Explorer og vælge kommandoen pakke, som vist nedenfor:

![Pakke et program med Visual Studio][vs-package-command]

Når emballagen er fuldført, kan du finde sted, hvor pakken i vinduet **Output** . Bemærk, at trinnet emballagen sker automatisk, når du installerer eller foretage fejlfinding af dit program i Visual Studio.

### <a name="test-the-package"></a>Teste pakken

Du kan kontrollere pakke strukturen lokalt via PowerShell ved hjælp af kommandoen **Test-ServiceFabricApplicationPackage** . Denne kommando skal kontrollere, om manifest parsing af problemer og bekræfte alle referencer. Bemærk, at denne kommando kun bekræfter strukturel korrekt gennemførelse af mapper og filer i pakken. Det kan ikke bekræfte nogen af kode eller data pakkens indhold ud over kontrollere, at alle nødvendige filer er til stede.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Denne fejl viser, at filen *MySetup.bat* , der refereres til i tjenestemanifestet **SetupEntryPoint** mangler fra kode pakken. Når den manglende fil er tilføjet, sender programmet bekræftelsen:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Når programmet er pakket korrekt og overfører kontrol, er det klar til installation.

## <a name="next-steps"></a>Næste trin

[Installere og fjerne programmer][10]

[Administrere programmets parametre for flere miljøer][11]

[RunAs: Kører en-strukturen tjenesteprogram med forskellige sikkerhedstilladelser][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
