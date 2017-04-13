<properties
   pageTitle="Om tjenesten strukturen programmet og service sikkerhedspolitikker | Microsoft Azure"
   description="En oversigt over, hvordan du kører en-strukturen tjenesteprogram under system og lokale sikkerhedskonti, herunder det SetupEntry punkt, hvor et program skal udføre nogle privilegerede handling, før den starter"
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
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Konfigurere sikkerhedspolitikker for dit program
Azure Service-strukturen giver mulighed for at sikre programmer, der kører i klynge under forskellige brugerkonti. Tjenesten strukturen sikrer også de ressourcer, som programmerne på tidspunktet for installation under brugerkontoen som filer, mapper og certifikater. Dette gør programmer, der kører, selv i en delt værtsmiljø sikker fra hinanden. 

Tjenesten strukturen programmer kører som standard under den konto, Fabric.exe-processen kører under. Tjenesten strukturen også giver mulighed for at køre programmer under en lokal brugerkonto eller lokale systemkonto, angivet i den progammanifest. Understøttede lokalt system kontotyper for er **LocalUser**, **Netværkstjeneste**, **LocalService**og **LocalSystem**.

 Når du kører Service-strukturen på Windows Server i dit datacenter, ved hjælp af enkeltstående installationsprogrammet, kan du bruge Active Directory (AD) domænekonti.

Brugergrupper kan være defineret og oprettet, så en eller flere brugere kan føjes til hver gruppe administreres sammen. Dette er nyttigt, når der er flere brugere til forskellige indgangspunkter og de skal have visse almindelige rettigheder, der er tilgængelige på grupperingsniveauet for.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Konfigurere politikken for tjenesten SetupEntryPoint

Som beskrevet i [programmet model](service-fabric-application-model.md) er **SetupEntryPoint** en privilegerede indgangspunkt, der kører med de samme legitimationsoplysninger som Service-strukturen (typisk kontoen *Netværkstjeneste* ), før alle andre indgangspunkt. Den eksekverbare fil, der er angivet af **indgangspunkt** er typisk Tjenesteværten længerevarende, så har du en separat opsætning startpunkt undgår at de skal køre den eksekverbare service vært med høj rettigheder til længere perioder. Den eksekverbare fil, der er angivet af **indgangspunkt** køres, når **SetupEntryPoint** afsluttet korrekt. Den resulterende proces overvåges og genstartet, begynder igen med **SetupEntryPoint**, hvis det nogensinde ophører eller går ned.

Følgende er en enkel service manifest eksempel, der viser SetupEntryPoint og det primære indgangspunkt til tjenesten.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Konfigurere politikken ved hjælp af en lokal konto

Når du konfigurerer tjenesten for at få en konfiguration startpunkt, kan du ændre de sikkerhedstilladelser, der køres i programmanifestet. Vist følgende eksempel viser, hvordan du konfigurerer tjenesten til at køre under bruger administratorrettigheder.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Opret først en **principper** sektion med et brugernavn, som SetupAdminUser. Dette angiver, at brugeren er medlem af gruppen Administratorer system.

Konfigurer en politik for at anvende denne hovedstolen **SetupEntryPoint**under afsnittet **ServiceManifestImport** . Det fortæller Service-strukturen, når filen **MySetup.bat** køres, at det skal være RunAs med administratorrettigheder. Da, hvor du har anvendt *ikke* en politik til det overordnede opslagsord punkt, koden i **MyServiceHost.exe** kører under system **Netværkstjeneste** konto. Dette er den standardkonto, alle indgangspunkter køres som.

Lad os nu føje filen MySetup.bat til Visual Studio-projekt til at teste administratorrettigheder. I Visual Studio, skal du højreklikke på tjenesten projektet og tilføje en ny fil kaldet MySetup.bat.

Derefter skal du kontrollere, at filen MySetup.bat er inkluderet i pakken tjeneste. Som standard er det ikke. Vælg fil, skal du højreklikke på for at få i genvejsmenuen, og vælg **Egenskaber**. Sørg for, **kopiere til Output Directory** er indstillet til at **kopiere Hvis nyere**i egenskabsdialogboksen. Se følgende skærmbillede.

![Visual Studio CopyToOutput for SetupEntryPoint batchfil][image1]

Åbn filen MySetup.bat nu, og Tilføj følgende kommandoer:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Derefter skal du opbygge og anvende løsningen til en lokal udvikling klynge.  Når tjenesten er startet, som vist i tjenesten strukturen Explorer, kan du se, at MySetup.bat lykkedes på to måder. Åbn en PowerShell-kommandoprompten, og skriv:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Derefter skal du notere navnet på noden, hvor tjenesten blev installeret og startet på Service-strukturen Explorer, for eksempel Node 2. Gå derefter til mappen program forekomst arbejde til at finde filen out.txt, der viser værdien af **TestVariable**. For eksempel hvis tjenesten blev installeret Node 2, og du kan gå til denne sti **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Konfigurere politikken ved hjælp af lokale systemkonti
Ofte er det bedst at køre scriptet til start ved hjælp af en lokal systemkonto i stedet for en administratorer konto som vist før. Kører på RunAs fungerer politik som administratorer typisk ikke godt, da maskiner har bruger Access kontrolelement (kontrol af Brugerkonti) som standard aktiveret. I så fald **anbefales det at køre SetupEntryPoint som LocalSystem i stedet for en lokal bruger, der er føjet til administratorgruppen**. I følgende eksempel viser, at SetupEntryPoint til at køre som LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Start PowerShell-kommandoer fra en SetupEntryPoint
Hvis du vil køre PowerShell fra punktet **SetupEntryPoint** , kan du køre **PowerShell.exe** i en batchfil, der peger på en PowerShell-fil. Først skal du tilføje en PowerShell-fil i tjenesten projektet, som **MySetup.ps1**. Husk at indstille egenskaben *kopiere Hvis nyere* , så filen er også inkluderet i pakken tjeneste. I følgende eksempel viser et eksempelbatchfil til at starte en PowerShell-fil kaldet MySetup.ps1, som angiver en system miljøvariablen kaldet **TestVariable**.


MySetup.bat til Start PowerShell-fil.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Tilføj følgende for at angive et system miljøvariablen i filen PowerShell:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Note:** Når batchfilen kører som standard ser den i programmappen kaldet **arbejde** til filer. I dette tilfælde, når MySetup.bat køres vil vi denne indstilling for at finde MySetup.ps1 i den samme mappe, som er programmappen **kode pakke** . For at ændre denne mappe skal du angive arbejdsmappen, som vist i følgende.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>Brug af konsolomdirigering til lokale fejlfinding
Nogle gange er det nyttigt at se console output fra kører et script til i forbindelse med fejlfinding. Du kan angive en console omdirigeringspolitik, der skriver output til en fil for at gøre dette. Fil output skrives til programmappen kaldet **log** på den node, hvor programmet er installeret og køre (se hvor du kan finde det i det foregående eksempel).

**Note: aldrig** bruge politikken console omdirigering i et program, der er implementeret i fremstilling, da det kan påvirke sekundære programmet på computeren. **Kun** Brug dette til lokale udvikling og fejlfinding formål.  

I følgende eksempel vises indstilling console omdirigering med en FileRetentionCount værdi.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Hvis du ændrer nu filen MySetup.ps1 for at skrive en **ekkoet** kommandoen, skriver dette til outputfilen forbindelse med fejlfinding.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Når du har foretages fejlfinding af dit script, med det samme Fjern denne console omdirigeringspolitik**

## <a name="configure-policy-for-service-code-packages"></a>Konfigurere politik for servicepakker kode 
I de foregående trin, du fik vist hvordan du anvender RunAs politik på SetupEntryPoint. Lad os se nærmere, hvordan du kan oprette forskellige principper, der kan anvendes som service politikker.

### <a name="create-local-user-groups"></a>Oprette lokale brugergrupper
Brugergrupper kan defineres og oprettet, som tillader en eller flere brugere, der skal føjes til en gruppe. Dette er særligt nyttigt, hvis der er flere brugere til forskellige indgangspunkter og de skal have visse almindelige rettigheder, der er tilgængelige på grupperingsniveauet for. I følgende eksempel viser en lokal gruppe med navnet **LocalAdminGroup** med administratorrettigheder. To brugere, Customer1 og Customer2, bliver medlemmer af denne lokale gruppe.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Oprette lokale brugere
Du kan oprette en lokal bruger, der kan bruges til at sikre en tjeneste i programmet. Når en **LocalUser** kontotype er angivet i afsnittet principper i programmanifestet, opretter tjenesten strukturen lokale brugerkonti på computere, hvor programmet er installeret. Som standard har disse konti ikke de samme navne som dem, der er angivet i programmanifestet (for eksempel, Customer3 i det følgende eksempel). I stedet de genereres dynamisk og har tilfældige adgangskoder.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Tildele politikker til kode servicepakker
Sektionen **RunAsPolicy** for en **ServiceManifestImport** angiver kontoen fra sektionen principper, der skal bruges til at køre en kode pakke. Det også knytter kode-pakker fra tjenestemanifestet brugerkonti i afsnittet principper. Du kan angive dette til konfiguration eller primære indgangspunkter, eller du kan angive `All` at anvende den på begge. I følgende eksempel viser forskellige politikker, som anvendes:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Hvis **EntryPointType** ikke er angivet, standard er indstillet til EntryPointType = "Hoved". Angive **SetupEntryPoint** er især nyttig, når du vil køre bestemte høj rettighed konfiguration handling under en systemkonto. Den faktiske tjenestekode kan køre under en nederste rettigheder konto.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Anvende en standardpolitik til alle servicepakker kode
Sektionen **DefaultRunAsPolicy** bruges til at angive en standard-brugerkonto for alle kode-pakker, der ikke har en bestemt **RunAsPolicy** defineret. Hvis de fleste af de kode-pakker, der er angivet i tjenestemanifestet bruges af et program skal køre under den samme bruger, kan programmet layoutene definerer en standardpolitik RunAs med brugerkontoen. I følgende eksempel angiver, at hvis en kode pakke ikke har en **RunAsPolicy** , der er angivet, er det kode pakken, skal køre under **MyDefaultAccount** angivet i sektionen principper.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>Ved hjælp af en Active Directory domain gruppe eller bruger
Du, kan køre tjenesten under legitimationsoplysningerne for en Active Directory (AD) bruger eller gruppekonto til tjenesten struktur, der er installeret på Windows Server med enkeltstående installer. Bemærk: Dette er Active Directory lokalt i dit domæne og er ikke med Azure Active Directory (AAD). Ved hjælp af en domænebruger eller gruppe, kan du derefter få adgang til andre ressourcer på domænet (for eksempel filshares), som har fået de nødvendige tilladelser.

I følgende eksempel viser en AD-bruger, der hedder *TestUser* med deres domæneadgangskode, der er krypteret med et certifikat, der hedder *MyCert*. Du kan bruge den `Invoke-ServiceFabricEncryptText` Powershell-kommando til at oprette hemmeligt krypteringstekst. Se denne artikel, [administrere hemmeligheder i tjenesten strukturen programmer](service-fabric-application-secret-management.md) få mere at vide om, hvordan. Privat nøgle for certifikatet til dekryptere adgangskoden skal være installeret på den lokale computer i en out-of-band-metode (i Azure dette er via ressourcestyring). Når tjenesten strukturen installerer servicepakke på computeren, er det kunne dekryptere hemmeligheden og sammen med brugernavnet, godkende med AD til at køre under disse legitimationsoplysninger.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Tildele SecurityAccessPolicy for HTTP og HTTPS slutpunkter
Hvis du anvender en politik for RunAs til en tjeneste, og tjenestemanifestet erklærer slutpunkt ressourcer med HTTP-protokollen, skal du angive en **SecurityAccessPolicy** for at sikre, at port, der tildeles disse slutpunkter er korrekt adgangskontrol er angivet for den RunAs brugerkonto, tjenesten kører under. Ellers **http.sys** har ikke adgang til tjenesten, og du får fejl med opkald fra klienten på computeren. Eksemplet vist følgende gælder kontoen Customer3 for et slutpunkt, kaldet **ServiceEndpointName**, give den fulde adgangsrettigheder.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Til HTTPS slutpunktet har du også angiver navnet på certifikatet for at vende tilbage til klienten. Du kan gøre dette ved hjælp af **EndpointBindingPolicy**med det certifikat, der er defineret i en certifikater i programmanifestet.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>En komplet programmet manifest eksempel
Følgende programmanifestet viser mange af de forskellige indstillinger:

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

* [Forstå programmet datamodellen](service-fabric-application-model.md)
* [Angive ressourcer i en tjenestemanifestet](service-fabric-service-manifest-resources.md)
* [Installere et program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
