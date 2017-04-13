<properties
    pageTitle="Hvad er en skybaseret tjeneste model og pakke | Microsoft Azure"
    description="I denne artikel beskrives skyen service modellen (.csdef, .cscfg) og i Azure-pakke (.cspkg)"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Hvad er modellen skybaseret tjeneste, og hvordan jeg pakke det?
En skybaseret tjeneste er oprettet ud fra tre komponenter, service definition _(.csdef)_, service config _(.cscfg)_og en tjeneste pakke _(.cspkg)_. Både **ServiceDefinition.csdef** og **ServiceConfig.cscfg** filer er XML-baserede og beskriver strukturen i skytjenesten, og hvordan den er konfigureret; samlet kaldes modellen. **ServicePackage.cspkg** er en zip-fil, der genereres fra **ServiceDefinition.csdef** og blandt andet, indeholder alle de påkrævede binær baseret afhængigheder. Azure opretter en skybaseret tjeneste fra både **ServicePackage.cspkg** og **ServiceConfig.cscfg**.

Når skytjenesten kører i Azure, kan du konfigurere det via filen **ServiceConfig.cscfg** , men du kan ikke ændre definitionen.

## <a name="what-would-you-like-to-know-more-about"></a>Hvad vil du gerne vide mere om?

* Jeg gerne vil vide mere om [ServiceDefinition.csdef](#csdef) og [ServiceConfig.cscfg](#cscfg) filerne.
* Jeg allerede vide noget om skal give mig [nogle eksempler](#next-steps) på, hvad kan jeg konfigurere.
* Jeg vil oprette [ServicePackage.cspkg](#cspkg).
* Jeg bruger Visual Studio, og jeg vil …
    * [Oprette en ny tjeneste i skyen][vs_create]
    * [Omkonfigurere en eksisterende skytjeneste][vs_reconfigure]
    * [Anvende en skybaseret tjeneste projektet][vs_deploy]
    * [Fjernskrivebord til en skybaseret tjenesteforekomst][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Filen **ServiceDefinition.csdef** angiver de indstillinger, der anvendes af Azure til at konfigurere en skybaseret tjeneste. [Azure Service Definition skema (.csdef filer)](https://msdn.microsoft.com/library/azure/ee758711.aspx) indeholder tilladte formatet til en tjeneste definitionsfil. I følgende eksempel viser de indstillinger, der kan være defineret for rollerne, internettet og arbejder:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Du kan referere til [Service Definition skema] [] til en bedre forståelse af XML-skemaet bruges her, men her er en hurtig gennemgang af nogle af elementerne:

**Websteder**  
Indeholder definitioner for websteder eller web-programmer, der er placeret i IIS7.

**InputEndpoints**  
Indeholder definitioner for slutpunkter, der bruges til at kontakte skytjenesten.

**InternalEndpoints**  
Indeholder definitioner for slutpunkter, der bruges af rolle forekomster til at kommunikere med hinanden.

**ConfigurationSettings**  
Indeholder definitioner af de indstillinger for funktioner for en bestemt rolle.

**Certifikater**  
Indeholder definitioner for certifikater, der skal bruges til en rolle. Det forrige kodeeksempel viser et certifikat, der bruges til konfiguration af Azure Connect.

**LocalResources**  
Indeholder definitioner for lokale lagerressourcer. Et lokalt lager ressource er en reserveret mappe i filsystemet på den virtuelle maskine, hvori der kører en forekomst af en rolle.

**Importerer**  
Indeholder definitioner for importerede moduler. Det forrige kodeeksempel viser modulerne for Fjernskrivebord og Azure forbinde.

**Start**  
Indeholder opgaver, der køres, når rollen starter. Opgaverne, der er defineret i en cmd. eller den eksekverbare fil.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurationen af indstillingerne for din skytjeneste bestemmes af værdierne i filen **ServiceConfiguration.cscfg** . Du angiver antallet forekomster, du vil installere for hver rolle i denne fil. Værdierne for de konfigurationsindstillinger, som du angav i tjenesten definitionsfil føjes til tjenesten konfigurationsfil. Thumbprints for en hvilken som helst management certifikater, der er knyttet til skytjenesten også tilføjes i filen. [Azure Service konfiguration skema (.cscfg filer)](https://msdn.microsoft.com/library/azure/ee758710.aspx) indeholder tilladte formatet til en tjeneste konfigurationsfil.

Konfigurationsfil tjenesten er ikke pakket med programmet, men der overføres til Azure som en separat fil og bruges til at konfigurere skytjenesten. Du kan overføre en ny tjeneste konfigurationsfil uden geninstallation skybaseret tjeneste. Konfiguration af værdierne for skytjenesten kan ændres, mens skytjenesten kører. I følgende eksempel vises de konfigurationsindstillinger, der kan være defineret for rollerne, internettet og arbejder:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Du kan referere til [Tjenesten konfiguration skema](https://msdn.microsoft.com/library/azure/ee758710.aspx) for bedre at forstå XML-skemaet bruges her, men her er en hurtig forklaring af elementerne:

**Forekomster**  
Konfigurerer antallet af aktive forekomster for rollen. Hvis du vil forhindre, at din skytjeneste potentielt bliver ikke tilgængelig under opgraderinger, er det anbefalet, at du installerer mere end én forekomst af dine web mod roller. Ved at gøre dette, du overholde retningslinjerne i [Azure beregne serviceaftale (SLA)](http://azure.microsoft.com/support/legal/sla/), som sikrer 99.95% eksterne forbindelse til internettet roller, når to eller flere forekomster af rolle er installeret til en tjeneste.

**ConfigurationSettings**  
Konfigurerer indstillingerne for de kørende forekomster til en rolle. Navnet på den `<Setting>` elementer, skal stemme overens indstilling definitioner i filen service definition.

**Certifikater**  
Konfigurerer certifikater, der anvendes af tjenesten. Det forrige kodeeksempel viser, hvordan du definerer certifikat til modulet Remote Access. Værdien for attributten *miniature* skal angives til miniature for certifikatet, du bruger.

<p/>

 >[AZURE.NOTE] Miniature for certifikatet, der kan føjes til konfigurationsfilen ved hjælp af en teksteditor, eller værdien, der kan tilføjes på fanen **certifikater** på siden **Egenskaber** for rollen i Visual Studio.



## <a name="defining-ports-for-role-instances"></a>Definere porte efter rolle forekomster
Azure kan kun én indgangspunkt til en web rolle. Det betyder, at der opstår al trafik via en IP-adresse. Du kan konfigurere dine websteder for at dele en port ved at konfigurere på host overskriften for at dirigere anmodningen til den korrekte placering. Du kan også konfigurere dine programmer for at lytte til kendte porte til IP-adressen.

I følgende eksempel viser konfigurationen for en web rolle med et websteds- og web-program. Webstedet er konfigureret som standardplaceringen for posten på port 80, og webprogrammerne er konfigureret til at modtage anmodninger fra en anden vært sidehoved, der kaldes "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Ændre konfigurationen af en rolle
Du kan opdatere konfigurationen af din skybaseret tjeneste, mens den kører i Azure, uden at tjenesten offline. Hvis du vil ændre konfigurationsoplysninger, kan du enten overføre en ny konfigurationsfil eller redigere konfigurationsfil sted og anvende den på din aktiv tjeneste. Følgende ændringer kan foretages af konfigurationen af en tjeneste:

- **Ændring af værdierne i indstillinger for søgekonfiguration**  
Når en konfiguration med ændringer, en forekomst af rolle kan vælge at anvende ændringen, mens forekomsten er online, eller hvis du vil genbruge forekomsten problemfrit og anvende ændringen under forekomsten er offline.

- **Ændre tjenesten topologien for rolle forekomster**  
Topologiændringer påvirker ikke kørende forekomster, undtagen hvor en forekomst fjernes. Alle resterende forekomster normalt behøver ikke at genbruges; dog kan du vælge at Papirkurv rolle forekomster som svar på en ændring af søgetopologien.

- **Ændre certifikat miniature**  
Du kan kun opdatere et certifikat, når en forekomst af rolle er offline. Hvis et certifikat er tilføjet, slettes eller ændres, mens en forekomst af rolle er online, tager Azure problemfrit forekomsten offline at opdatere certifikatet, og finde den online igen, når ændringen er fuldført.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Håndtering af ændringer i konfigurationen med tjenesten Runtime begivenheder
[Azure Runtime bibliotek](https://msdn.microsoft.com/library/azure/mt419365.aspx) indeholder navneområdet [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) , som indeholder klasser til arbejde med Azure miljøet fra kode, der kører i en forekomst af en rolle. Klassen [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) definerer de følgende hændelser, der er opløftet før og efter en konfigurationsændring af:

- **[Ændre](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) begivenhed**  
Dette sker, før ændringen konfiguration er anvendt på en bestemt forekomst af en rolle, hvorved du får mulighed for at tage ned rolle forekomster, hvis det er nødvendigt.
- **[Ændrede](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) begivenhed**  
Sker, når konfiguration ændringen er anvendt på en bestemt forekomst af en rolle.

> [AZURE.NOTE] Da certifikat ændringer tage altid forekomster af en rolle på offline, de ikke optage RoleEnvironment.Changing eller RoleEnvironment.Changed begivenheder.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Hvis du vil installere et program som en skybaseret tjeneste i Azure, skal du først pakke programmet i det pågældende format. Du kan bruge værktøjet **CSPack** kommandolinjen (installeret med [Azure SDK](https://azure.microsoft.com/downloads/)) til at oprette pakkefilen som et alternativ til Visual Studio.

**CSPack** bruger indholdet af tjenesten definitionsfil og service konfigurationsfil til at definere indholdet af pakken. **CSPack** genererer en pakke programfil (.cspkg), du kan overføre til Azure ved hjælp af [Azure-portalen](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Som standard pakken hedder `[ServiceDefinitionFileName].cspkg`, men du kan angive et andet navn ved hjælp af den `/out` mulighed for **CSPack**.

**CSPack** er normalt placeret på  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (på windows) findes ved at køre genvejen til **Microsoft Azure kommandoprompten** , der er installeret med SDK.  
>  
>Kør programmet CSPack.exe alene kan se dokumentationen til om alle de mulige parametre og kommandoer.

<p />

>[AZURE.TIP]
Køre skybaseret tjeneste lokalt i **Microsoft Azure beregne Emulator**, skal du bruge indstillingen **/copyonly** denne indstilling kopierer de binære filer til programmet til et katalog layout, hvorfra de kan køres i Beregn emulatoren.

### <a name="example-command-to-package-a-cloud-service"></a>Eksempel på kommandoen til at pakke en skybaseret tjeneste
I følgende eksempel oprettes en programpakke, der indeholder oplysninger til en web rolle. Kommandoen angiver definitionsfil tjeneste til at bruge den mappe, hvor binære filer kan findes, og navnet på pakkefilen.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Hvis programmet indeholder både en web rolle og en kollega rolle, bruges følgende kommando:

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Hvor variablerne, der er defineret som følger:

| Variabel                  | Værdi |
| ------------------------- | ----- |
| \[DirectoryName\]         | Undermappen under mappen rod projekt, der indeholder filen .csdef af Azure projektet.|
| \[ServiceDefinition\]     | Navnet på den tjeneste definitionsfil. Som standard er denne fil med navnet ServiceDefinition.csdef.  |
| \[OutputFileName\]        | Navnet på den oprettede pakkefil. Dette er typisk angivet til navnet på programmet. Hvis ikke angives noget filnavn, oprettes programpakken som \[ApplicationName\].cspkg.|
| \[RoleName\]              | Navnet på rollen, som defineret i definitionsfil service.|
| \[RoleBinariesDirectory] | Placeringen af de binære filer for rollen.|
| \[VirtualPath\]           | De fysiske mapper for hver virtuelle sti, der er defineret i afsnittet websteder i definitionen af tjenesten.|
| \[PhysicalPath\]          | De fysiske mapper af indhold for hver virtuelle sti, der er defineret i noden for det område af definitionen af tjenesten.|
| \[RoleAssemblyName\]      | Navnet på den binære fil for rollen.| 


## <a name="next-steps"></a>Næste trin

Jeg opretter en skybaseret tjeneste pakke, og jeg vil...

* [Konfiguration af Fjernskrivebord til en skybaseret tjenesteforekomst][remotedesktop]
* [Anvende en skybaseret tjeneste projektet][deploy]

Jeg bruger Visual Studio, og jeg vil …

* [Oprette en ny tjeneste i skyen][vs_create]
* [Omkonfigurere en eksisterende skytjeneste][vs_reconfigure]
* [Anvende en skybaseret tjeneste projektet][vs_deploy]
* [Konfiguration af Fjernskrivebord til en skybaseret tjenesteforekomst][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
