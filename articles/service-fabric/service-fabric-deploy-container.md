<properties
   pageTitle="Service-strukturen og implementering beholdere | Microsoft Azure"
   description="Service-strukturen og brug af objektbeholdere at implementere microservice programmer. I denne artikel beskrives de funktioner, der indeholder Service-strukturen af objektbeholdere og hvordan du installerer et Windows objektbeholder billede i en klynge"
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
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Eksempel: Installere en Windows-objektbeholder til tjenesten struktur

> [AZURE.SELECTOR]
- [Installere Windows objektbeholder](service-fabric-deploy-container.md)
- [Installere Docker objektbeholder](service-fabric-deploy-container-linux.md)

I denne artikel fører dig gennem oprettelsen bulkvareenhed tjenester i Windows beholdere. 

>[AZURE.NOTE] Denne funktion er i Vis udskrift for Linux og ikke tilgængelige til brug sammen med Windows Server 2016. Dette vil være tilgængelige i preview til Windows Server 2016 i den næste udgave af Service-strukturen og understøttede i den efterfølgende udgave bagefter.

Tjenesten strukturen har flere objektbeholder-funktioner, der hjælper dig med at opbygge programmer, der består af microservices, der er bulkvareenhed. Disse kaldes bulkvareenhed tjenester. 

Funktionerne omfatter;

- Objektbeholder billede installation og aktivering
- Ressource styring
- Lager godkendelse
- Objektbeholder port til host porttilknytning
- Objektbeholder-objektbeholder registrering og kommunikation
- Mulighed for at konfigurere og angive miljøvariabler

Giver mulighed for at se på de enkelte funktioner igen, når emballagen en bulkvareenhed service medtages i dit program.

## <a name="packaging-a-windows-container"></a>Pakke en Windows-objektbeholder

Når emballagen en objektbeholder, kan du vælge enten at bruge en skabelon til Visual Studio project eller [oprette programpakken manuelt](#manually). Brug af Visual Studio, er programmet pakke strukturen og manifestfilerne oprettet af guiden project for dig (dette kommer i den næste version).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>Brug af Visual Studio til at pakke en eksisterende afbildning objektbeholder

>[AZURE.NOTE] I en fremtidig udgave af det Visual Studio værktøjer SDK, vil du kunne tilføje en objektbeholder til programmet på samme måde, du kan tilføje gæst eksekverbare i dag. [Implementer gæst eksekverbare til tjenesten strukturen](service-fabric-deploy-existing-app.md) i emnet. I øjeblikket skal du gøre manuel pakke, som beskrevet nedenfor.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Manuelt pakke og implementere en objektbeholder
Processen for at manuelt pakke en bulkvareenhed tjeneste er baseret på følgende trin:

1. Publiceret beholdere til lageret.
2. Oprette mappestrukturen pakke.
3. Rediger den tjenestemanifestfilen.
4. Redigere manifestfilen til programmet.

## <a name="container-image-deployment-and-activation"></a>Objektbeholder billede installation og aktivering.
I tjenesten strukturen [programmet model](service-fabric-application-model.md)repræsenterer en objektbeholder en program-vært, hvor flere service replikaer er placeret. Hvis du vil installere og aktivere en objektbeholder, sætte navnet på et objektbeholder skærmbillede i en `ContainerHost` element i tjenestemanifestet.

I tjenestemanifestet tilføje en `ContainerHost` for posten peg og angive den `ImageName` er navnet på objektbeholder lager og billede. Følgende delvis manifestet viser et eksempel med at udrulle objektbeholderen hedder *myimage:v1* fra et lager, kaldet *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Du kan angive input kommandoer til objektbeholder billedet ved at angive det valgfri `Commands` element med et komma afgrænset sæt kommandoer til at køre i objektbeholderen. 

## <a name="resource-governance"></a>Ressource styring
Ressource styring er en funktion af beholderen og begrænser de ressourcer, der kan bruge objektbeholderen på værten. Den `ResourceGovernancePolicy`, angivet i programmanifestet, giver mulighed for at definere ressource begrænsninger for en servicepakke kode. Begrænsninger for ressource kan angives for;

- Hukommelse
- MemorySwap
- CpuShares (CPU relative vægt)
- MemoryReservationInMB  
- BlkioWeight (BlockIO relative vægt). 

>[AZURE.NOTE] I en senere version support for at angive bestemte Bloker EY grænser såsom IOP'er, læse-og skriveadgang BPS og andre vil være muligt.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Lager godkendelse
Du skal muligvis angive logonoplysninger til objektbeholder lager for at hente en objektbeholder. Logonoplysninger, der er angivet i *programmanifestet* bruges til at angive logonoplysninger eller SSH tast, skal du downloade objektbeholder billedet fra billede-lager.  I følgende eksempel viser en konto med navnet *TestUser* sammen med adgangskoden i klar tekst. Det anbefales **ikke** .


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Adgangskoden, kan og skal krypteres ved hjælp af et certifikat, der er installeret på maskinen.

I følgende eksempel viser en konto med navnet *TestUser* med den adgangskode, der er krypteret med et certifikat, der hedder *MyCert*. Du kan bruge den `Invoke-ServiceFabricEncryptText` Powershell-kommando til at oprette hemmeligt kryptering teksten til adgangskoden. Se denne artikel, [administrere hemmeligheder i tjenesten strukturen programmer](service-fabric-application-secret-management.md) få mere at vide om, hvordan. Privat nøgle for certifikatet til dekryptere adgangskoden skal være installeret på den lokale computer i en out-of-band-metode (i Azure dette er via ARM). Når tjenesten strukturen installerer servicepakke på computeren, er det kunne dekryptere hemmeligheden og sammen med kontonavnet, godkende med objektbeholderen lageret ved hjælp af disse legitimationsoplysninger.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Objektbeholder port til host porttilknytning
Du kan konfigurere en host port, der bruges til at kommunikere med objektbeholderen ved at angive en `PortBinding` i progammanifest. Portbinding knytter port tjenesten lytter på i beholderen til en port på værten.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Objektbeholder-objektbeholder registrering og kommunikation
Ved hjælp af den `PortBinding` politik kan du knytte en objektbeholder port til en `Endpoint` i tjenestemanifestet som vist i følgende eksempel. Slutpunktet `Endpoint1` kan angive en fast port, for eksempel port 80 eller angive ingen port overhovedet, i hvilket tilfælde en tilfældig port fra de klynger programmet portområde er valgt for dig.

Af gæst objektbeholdere, der angiver en `Endpoint` som dette i tjenestemanifestet gør det muligt for tjenesten strukturen publicere dette slutpunkt til tjenesten Naming automatisk, så andre tjenester, der kører i klyngen kan finde denne objektbeholder ved hjælp af Løs service RESTEN forespørgsler. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Når du registrerer med Naming-tjenesten, kan du nemt gøre objektbeholder-objektbeholder kommunikation i koden i din objektbeholder ved hjælp af [omvendt proxy](service-fabric-reverseproxy.md). Alt, skal du gøre, er giver omvendt proxy http lytte port og navnet på de tjenester, du vil kommunikere med ved at angive dem som miljøvariabler. Se næste afsnit om, hvordan du gør dette.  

## <a name="configure-and-set-environment-variables"></a>Konfigurere og angive miljøvariabler
Miljøvariabler kan være angivet fjenden hver kode pakke i tjenesten manifestet for begge tjenester, der er implementeret i beholdere eller som processer/gæst eksekverbare filer. Disse miljø variable værdier kan tilsidesættes specifikt i programmanifestet eller angivet under installationen som programmets parametre.

De følgende tjenestemanifestet XML-kodestykke viser et eksempel på, hvordan du kan angive miljøvariabler for en kode pakke. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Variablerne miljø kan tilsidesættes på niveauet for programmet manifest:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

I ovennævnte eksempel skal vi har angivet en eksplicit værdi for den `HttpGateway` miljøvariablen (19000) under værdien for `BackendServiceName` parameter er angivet den `[BackendSvc]` program parameter. Dette gør det muligt at angive værdien for `BackendServiceName`værdi på tidspunktet for installation af programmet og ikke har en fast værdi i manifestet. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Fuldføre eksempler på program og tjenestemanifestet
Følgende er et eksempel progammanifest, der viser objektbeholder funktionerne.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


Følgende er et eksempel tjenestemanifestet (angivet i den foregående programmanifest), der viser funktionerne objektbeholder

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
