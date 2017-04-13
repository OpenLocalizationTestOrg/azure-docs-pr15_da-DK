<properties
   pageTitle="Bruge Windows PowerShell-Scripts til at udgive Udviklingscenter og testmiljøer | Microsoft Azure"
   description="Lær at bruge Windows PowerShell-scripts fra Visual Studio til at publicere til udvikling og teste miljøer."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Bruge Windows PowerShell-scripts til at publicere til Udviklingscenter og teste miljøer

Når du opretter et webprogram i Visual Studio, kan du oprette et Windows PowerShell-script, som du senere kan bruge til at automatisere udgivelse af dit websted til Azure som en Web App i Azure App Service eller en virtuel maskine. Du kan redigere og udvide Windows PowerShell-script i Visual Studio editoren efter dine behov, eller du kan integrere scriptet med eksisterende build, test og publicere scripts.

Brug af disse scripts, kan du klargøre tilpassede versioner (også kaldet Udviklingscenter og test miljøer) på dit websted til midlertidige brug. For eksempel kan du konfigurere en bestemt version af dit websted på en Azure virtuelt eller på den midlertidige slot på et websted til at køre en test-pakke, reproducere en fejl, teste en rettelse, prøveversion en foreslået ændring eller konfigurere en brugerdefineret miljø til et demo eller en præsentation. Når du har oprettet et script, hvor der publiceres dit projekt, kan du genskabe identiske miljøer ved at køre scriptet igen, efter behov, eller du kan køre scriptet med dine egne build for dit webprogram til at oprette en brugerdefineret testmiljø.

## <a name="what-you-need"></a>Hvad du bør

- Azure SDK 2.3 eller nyere. Yderligere oplysninger finder du i [Visual Studio-overførsler](http://go.microsoft.com/fwlink/?LinkID=624384) .

Du behøver ikke Azure SDK til at generere scriptene for webprojekter. Denne funktion er i webprojekter, som ikke web roller i skytjenester.

- Azure PowerShell 0.7.4 eller nyere. Se, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md) kan finde flere oplysninger.

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) eller nyere.

## <a name="additional-tools"></a>Yderligere værktøjer

Yderligere værktøjer og ressourcer til at arbejde med PowerShell i Visual Studio til Azure udvikling er tilgængelige. Se [PowerShell Tools til Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Opretter Publicer scripts

Du kan generere Publicer scriptene for en virtuel maskine, der er vært dit websted, når du opretter et nyt projekt ved følgende [disse instruktioner](./virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md). Du kan også [generere publicere scripts til webapps i Azure App Service](./app-service-web/web-sites-dotnet-get-started.md).

## <a name="scripts-that-visual-studio-generates"></a>Scripts, som genererer Visual Studio

Visual Studio genererer en løsning på brugerniveau mappe med navnet **PublishScripts** , der indeholder to Windows PowerShell-filer, et Udgiv script til din virtuelt eller websted, og et modul, der indeholder funktioner, der kan bruges i scriptene. Visual Studio genererer også en fil i formatet JSON, der angiver detaljerne for det projekt, du installerer.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell publicere script

Publicer scriptet indeholder trin til bestemte Udgiv til implementering af et websted eller virtuelt. Visual Studio indeholder syntaksen farvelægning til udvikling af Windows PowerShell. Hjælp til funktionerne, der er tilgængelig, og du kan frit redigere funktionerne i scriptet så det passer til dine krav til ændring.

### <a name="windows-powershell-module"></a>Windows PowerShell-modulet

Windows PowerShell-modulet, som Visual Studio genererer indeholder funktioner, der bruger scriptet Publicer. Disse er Azure PowerShell funktioner og er ikke beregnet til at være ændret. Se, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md) kan finde flere oplysninger.

### <a name="json-configuration-file"></a>JSON konfigurationsfil

Filen JSON oprettes i mappen **konfigurationer** og indeholder konfigurationsdata, der angiver præcis de ressourcer, der skal installeres til Azure. Navnet på den fil, genererer Visual Studio er project-navn-WAWS-dev.json Hvis du oprettede et websted eller project navn-VM-dev.json, hvis du har oprettet en virtuel maskine. Her er et eksempel på en JSON-konfigurationsfil, som genereres, når du opretter et websted. De fleste af værdierne, der er ingen forklaring. Webstedets navn genereres af Azure, så den ikke kan svarer til dit projektnavn.

```
{
"environmentSettings": {
"webSite": {
"name": "WebApplication26632",
"location": "West US"
},
"databases": [
{
"connectionStringName": "DefaultConnection",
"databaseName": "WebApplication26632_db",
"serverName": "YourDatabaseServerName",
"user": "sqluser2",
"password": "",
"edition": "",
"size": "",
"collation": "",
"location": "West US"
}
]
}
}
```
Når du opretter en virtuel maskine, ser ud som følger JSON-konfigurationsfil. Bemærk, at der oprettes en skybaseret tjeneste som en beholder for den virtuelle maskine. Den virtuelle maskine indeholder den sædvanlige slutpunkter for Webadgang via HTTP og HTTPS samt slutpunkter for Web installerer, hvor du kan udgive på webstedet fra din lokale computer, Fjernskrivebord og Windows PowerShell.

```
{
"environmentSettings": {
"cloudService": {
"name": "myusernamevm1",
"affinityGroup": "",
"location": "West US",
"virtualNetwork": "",
"subnet": "",
"availabilitySet": "",
"virtualMachine": {
"name": "myusernamevm1",
"vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
"size": "Small",
"user": "vmuser1",
"password": "",
"enableWebDeployExtension": true,
"endpoints": [
{
"name": "Http",
"protocol": "TCP",
"publicPort": "80",
"privatePort": "80"
},
{
"name": "Https",
"protocol": "TCP",
"publicPort": "443",
"privatePort": "443"
},
{
"name": "WebDeploy",
"protocol": "TCP",
"publicPort": "8172",
"privatePort": "8172"
},
{
"name": "Remote Desktop",
"protocol": "TCP",
"publicPort": "3389",
"privatePort": "3389"
},
{
"name": "Powershell",
"protocol": "TCP",
"publicPort": "5986",
"privatePort": "5986"
}
]
}
},
"databases": [
{
"connectionStringName": "",
"databaseName": "",
"serverName": "",
"user": "",
"password": ""
}
],
"webDeployParameters": {
"iisWebApplicationName": "Default Web Site"
}
}
}
```

Du kan redigere JSON konfigurationen for at ændre, hvad sker der, når du kører Publicer scripts. Den `cloudService` og `virtualMachine` sektioner er påkrævet, men du kan slette den `databases` afsnit, hvis du ikke har brug for den. De egenskaber, der er tomme i standard-konfigurationsfil, som Visual Studio genererer er valgfri. dem, der har værdier i filen standard konfiguration er påkrævet.

Hvis du har et websted, der indeholder flere installation miljøer (kaldet pladser) i stedet for en enkelt produktionslokation i Azure, kan du medtage slot-navn navnet på det offentlige websted i JSON-konfigurationsfil. Eksempelvis hvis du har et websted, der har navnet **Mit websted** og en plads til det navngivne **teste** derefter URI er mit websted-test.cloudapp.net, men det rigtige navn til brug i konfigurationsfil er mysite(test). Du kan kun gøre dette, hvis webstedet, og pladser findes allerede i dit abonnement. Hvis de ikke findes, oprette webstedet ved at køre scriptet uden at angive slotten, og derefter oprette slotten i [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885)og derefter køre scriptet med ændret webstedets navn. Se [konfigurere arrangere miljøer til webapps i Azure App Service](./app-service-web/web-sites-staged-publishing.md)kan finde flere oplysninger om installation pladser til online.

## <a name="how-to-run-the-publish-scripts"></a>Sådan køres Publicer scripts

Hvis du har aldrig kører et Windows PowerShell-script før, skal du først angive udførelse af politikken til at aktivere til at køre scripts. Dette er en sikkerhedsfunktion, for at forhindre brugere i at køre Windows PowerShell-scripts, hvis de er udsatte malware eller virus, der involverer udfører scripts.

### <a name="run-the-script"></a>Køre scriptet

1. Oprette pakken Web installere for projektet. En Web installere pakke er en komprimeret arkiv (.zip-fil), der indeholder filer, som du vil kopiere til dit websted eller virtuelt. Du kan oprette Web installere pakker i Visual Studio for et webprogram.

![Oprette Web installere pakke](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Du kan finde flere oplysninger, se [Sådan: oprette en Web-installationspakke i Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Du kan også automatisere oprettelsen af din Web installere pakke, som beskrevet i afsnittet **tilpasse og udvide Publicer scripts** senere i dette emne.

1. I **Solution Explorer**skal du åbne genvejsmenuen for scriptet og derefter vælge **Åbn med PowerShell ISE**.

1. Hvis dette er første gang du har kørt Windows PowerShell-scripts på denne computer, Åbn et kommandopromptvindue med administratorrettigheder, og Skriv følgende kommando:

`Set-ExecutionPolicy RemoteSigned`

1. Log på Azure ved hjælp af følgende kommando.

`Add-AzureAccount`

Når du bliver bedt om det, kan du angive dit brugernavn og din adgangskode.

Bemærk, at når du automatisere scriptet, denne metode til at formidle Azure legitimationsoplysninger ikke virker. I stedet skal du bruge filen .publishsettings til at angive legitimationsoplysninger. Én gang, du bruge kommandoen **Get-AzurePublishSettingsFile** til at hente filen fra Azure, og derefter bruge **Importér AzurePublishSettingsFile** til at importere filen. Finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md).

1. (Valgfrit) Hvis du vil oprette Azure ressourcer som virtuelt, database og websted uden at udgive dit webprogram, skal du bruge kommandoen **Publicer WebApplication.ps1** med den **-konfiguration** argumentet indstillet til JSON-konfigurationsfil. Denne kommandolinje bruger JSON-konfigurationsfil til at bestemme, hvilke ressourcer til at oprette. Fordi den bruger standardindstillingerne for andre kommandolinjeargumenter, opretter ressourcerne, men udgive ikke dit webprogram. Delen-detaljeret indstilling giver dig flere oplysninger om, hvad der sker.

`Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. Bruge kommandoen **Publicer WebApplication.ps1** som vist i et af følgende eksempler at kalde scriptet og publicere dit webprogram. Hvis du vil tilsidesætte standardindstillingerne for nogen af de andre argumenter, som Abonnementsnavnet, udgive pakkenavn, virtuelt legitimationsoplysninger eller database server-legitimationsoplysninger, kan du angive disse parametre. Brug den **– detaljeret** mulighed for at se flere oplysninger om status i en udgivelsesproces.

```
Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
–SubscriptionName Contoso `
-WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
-DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
-Verbose
```

Hvis du opretter en virtuel maskine, kommandoen ser ud som følgende. I dette eksempel vises også at angive legitimationsoplysninger for flere databaser. Til de virtuelle maskiner, disse scripts opretter, er SSL-certifikatet ikke fra et rodnøglecenter. Derfor skal du bruge indstillingen **– AllowUntrusted** .

```
Publish-WebApplication.ps1 `
-Configuration C:\Path\ADVM-VM-test.json `
-SubscriptionName Contoso `
-WebDeployPackage C:\Path\ADVM.zip `
-AllowUntrusted `
-VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
-DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
-Verbose
```

Scriptet kan oprette databaser, men det oprette ikke databaseservere. Hvis du vil oprette en databaseserver, kan du bruge funktionen **Ny AzureSqlDatabaseServer** i modulet Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Tilpasse og udvide Publicer scripts

Du kan tilpasse Publicer script og JSON konfigurationsfil. Funktionerne i Windows PowerShell-modulet **AzureWebAppPublishModule.psm1** er ikke beregnet til at være ændret. Hvis du blot ønsker at angive en anden database eller ændre nogle af egenskaberne for den virtuelle maskine, kan du redigere JSON-konfigurationsfil. Hvis du vil udvider funktionaliteten i scriptet til at automatisere opbygge og teste dit projekt, kan du kan implementere funktionen følgebrev i **Publicer WebApplication.ps1**.

Hvis du vil automatisere opbygning af dit projekt, tilføje kode, der kalder MSBuild til `New-WebDeployPackage` som vist i dette eksempel på en kode. Stien til kommandoen MSBuild er forskellige, afhængigt af versionen af Visual Studio, du har installeret. Du kan bruge funktionen **Get-MSBuildCmd**, for at få den korrekte sti, som vist i dette eksempel.

### <a name="to-automate-building-your-project"></a>Til at automatisere opbygning af dit projekt

1. Tilføje den `$ProjectFile` parameter i sektionen global parameteren.

```
[Parameter(Mandatory = $false)]
  [ValidateScript({Test-Path $_ -PathType Leaf})]
  [String]
  $ProjectFile,
```

1. Kopiere funktionen `Get-MSBuildCmd` i en scriptfil.

```
function Get-MSBuildCmd
{
        process
{

             $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                   Sort-Object {[double]$_.PSChildName} -Descending |
                                   Select-Object -First 1 |
                                   Get-ItemProperty -Name MSBuildToolsPath |
                                   Select -ExpandProperty MSBuildToolsPath
       
            $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

        return Get-Item $path
    }
}
```

1. Erstatte `New-WebDeployPackage` med følgende kode og erstatte pladsholderne i linjen bygning af `$msbuildCmd`. Denne kode er til Visual Studio-2015. Hvis du bruger Visual Studio 2013, ændre egenskaben **VisualStudioVersion** under til `12.0`.

```
function New-WebDeployPackage
{
    #Write a function to build and package your web application
      
#To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
      
Write-VerboseWithTime 'Build-WebDeployPackage: Start'
      
$msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
      
Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
      
#Start execution of the build command
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
      
if ($job.ExitCode -ne 0)
{
throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName
      
#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
      
      
#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
      
Write-VerboseWithTime 'Build-WebDeployPackage: End'
      
return $WebDeployPackage
}
```

1. Ringe til den `New-WebDeployPackage` funktionen før denne linje: `$Config = Read-ConfigFile $Configuration` til webapps eller `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` til virtuelle computere.

```
if($ProjectFile)
{
$WebDeployPackage = New-WebDeployPackage
}
```

1. Aktivere tilpassede scriptet fra kommandolinjen ved hjælp af overførsel af `$Project` argument, som i følgende eksempel på kommandolinjen.

```
.\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
-ProjectFile ..\WebApplication5\WebApplication5.csproj `
-VMPassword @{Name="VMUser";Password="Test.123"} `
-AllowUntrusted `
-Verbose
```

Hvis du vil automatisere test af dit program, tilføje kode for at `Test-WebApplication`. Sørg for at Fjern kommentar fra linjerne i **Publicer WebApplication.ps1** hvor disse funktioner kaldes. Hvis du ikke angiver en implementering, kan du manuelt opbygge dit projekt med Visual Studio og derefter køre scriptet Publicer publicere til Azure.

## <a name="publishing-function-summary"></a>Publicering af funktionen oversigt

Hvis du vil have hjælp til funktioner, du kan bruge Windows PowerShell kommandoprompten, skal du bruge kommandoen `Get-Help function-name`. Hjælp indeholder parameter Hjælp og eksempler. Den samme hjælpetekst er også i kildefiler script, **AzureWebAppPublishModule.psm1** og **Udgiv WebApplication.ps1**. Script og Hjælp er lokaliseret i dit Visual Studio-sprog.

**AzureWebAppPublishModule**

|Funktionsnavn|Beskrivelse|
|---|---|
|Tilføje AzureSQLDatabase|Opretter en ny Azure SQL-database.|
|Tilføje AzureSQLDatabases|Opretter Azure SQL-databaser fra værdierne i filen JSON configuation, som genererer Visual Studio.|
|Tilføje AzureVM|Opretter en Azure virtuel maskine, og returnerer den udløst VM URL-adressen. Funktionen konfigurerer forudsætninger og derefter kalder funktionen **Ny AzureVM** (Azure modul) for at oprette en ny virtuel maskine.|
|Tilføje AzureVMEndpoints|Føjer nye input slutpunkter til en virtuel maskine, og returnerer den virtuelle maskine med det nye slutpunkt.|
|Tilføje AzureVMStorage|Opretter en ny Azure-lager-konto i det aktuelle abonnement. Navnet på kontoen, der begynder med "devtest" efterfulgt af en entydig alfanumerisk streng. Funktionen returnerer navnet på den nye konto lagerplads. Du skal angive en placering eller en forbindelse gruppe til den nye konto lagerplads.|
|Tilføje AzureWebsite|Opretter et websted med angivne navn og placering. Denne funktion kalder funktionen **Ny AzureWebsite** i modulet Azure. Hvis abonnementet ikke allerede indeholder et websted med det angivne navn, opretter webstedet denne funktion, og returnerer et websted objekt. Ellers returneres `$null`.|
|Sikkerhedskopi-abonnement|Gemmer det aktuelle Azure abonnement i den `$Script:originalSubscription` variable i script omfang. Denne funktion gemmer det aktuelle Azure abonnement (som der fås ved at `Get-AzureSubscription -Current`) og dens lagerplads konto, og det abonnement, ændres med dette script (gemt i variablen `$UserSpecifiedSubscription`) og dens lagerplads konto, i script omfang. Ved at gemme værdierne, kan du bruge en funktion, såsom `Restore-Subscription`, for at gendanne den oprindelige aktuelle abonnement og lager konto til den aktuelle status, hvis den aktuelle status er ændret.|
|Find AzureVM|Får den angivne Azure virtuelle maskine.|
|Formatér DevTestMessageWithTime|Føjer dato og klokkeslæt i en meddelelse. Denne funktion er udviklet til meddelelser, der skrives til fejl og detaljeret streams.|
|Get-AzureSQLDatabaseConnectionString|Samler en forbindelsesstreng til at oprette forbindelse til en Azure SQL-database.|
|Get-AzureVMStorage|Returnerer navnet på den første lagerplads konto med navnet mønster "devtest*" (og små bogstaver) i den angivne placering eller forbindelse gruppe. Hvis den "devtest*" lagerplads konto ikke stemmer overens med den placering eller forbindelse gruppe, funktionen ignorerer den. Du skal angive en placering eller en forbindelse gruppe.|
|Get-MSDeployCmd|Returnerer en kommando til at køre værktøjet MsDeploy.exe.|
|Ny AzureVMEnvironment|Finder eller opretter en virtuel maskine i det abonnement, der svarer til værdierne i JSON-konfigurationsfil.|
|Publicere WebPackage|Publicere pakke, bruger MsDeploy.exe og et websted. ZIP-filen til at udrulle ressourcer til et websted. Denne funktion Generer ikke noget output. Hvis opkaldet til MSDeploy.exe mislykkes, medfører funktionen en undtagelse. For at få mere detaljerede output skal bruge den **-detaljeret** indstilling.|
|Publicere WebPackageToVM|Bekræfter parameterværdierne og derefter kalder funktionen **Publicer WebPackage** .|
|Læs ConfigFile|Konfigurationsfil JSON har valideret, og returnerer en hashtabel af valgte værdier.|
|Gendan-abonnement|Nulstiller det aktuelle abonnement til det oprindelige abonnement.|
|Test-AzureModule|Returnerer `$true` Hvis installerede Azure modul version 0.7.4 eller nyere. Returnerer `$false` hvis modulet, der ikke er installeret eller er en tidligere version. Denne funktion ikke har nogen parametre.|
|Test-AzureModuleVersion|Returnerer `$true` Hvis versionen af Azure modulet er 0.7.4 eller nyere. Returnerer `$false` hvis modulet, der ikke er installeret eller er en tidligere version. Denne funktion ikke har nogen parametre.|
|Test-HttpsUrl|Konverterer input URL-adressen til et System.Uri objekt. Returnerer `$True` Hvis URL-adressen er absolut og dens farveskema er https. Returnerer `$false` Hvis URL-adressen er relativ, dens farveskema ikke HTTPS eller input strengen kan ikke konverteres til en URL-adresse.|
|Test medlem|Returnerer `$true` Hvis en egenskab eller metode er medlem af objektet. Ellers returnerer `$false`.|
|Skriv ErrorWithTime|Skriver en fejlmeddelelse om præfikset med det aktuelle klokkeslæt. Denne funktion kalder funktionen **Format DevTestMessageWithTime** for at føjes tid, før Skriv meddelelsen til denne fejl stream.|
|Skriv HostWithTime|Skriver en meddelelse til værtsprogram (**Skriv Host**) præfikset med det aktuelle klokkeslæt. Varierer effekten af at oprettelsen af host-programmet. De fleste programmer, der er vært Windows PowerShell skrive disse meddelelser til standard output.|
|Skriv VerboseWithTime|Skriver en detaljeret meddelelse præfikset med det aktuelle klokkeslæt. Fordi det kalder **Skriv detaljeret**, vises meddelelsen kun, når scriptet kører med parameteren **detaljeret** , eller når indstillingerne for **VerbosePreference** er angivet til **Fortsæt**.|

**Publicere webprogrammet**

|Funktionsnavn|Beskrivelse|
|---|---|
|Ny AzureWebApplicationEnvironment|Opretter Azure ressourcer, som et websted eller virtuelt.|
|Ny WebDeployPackage|Denne funktion er ikke implementeret. Du kan tilføje kommandoer i denne funktion til at opbygge dit projekt.|
|Publicere AzureWebApplication|Udgiver et webprogram til Azure.|
|Publicere webprogrammet|Opretter og installerer Web Apps, virtuelle maskiner, SQL-databaser og lagerplads konti for et Visual Studio webprojekt.|
|Test-webprogrammet|Denne funktion er ikke implementeret. Du kan tilføje kommandoer i denne funktion til at teste dit program.|

## <a name="next-steps"></a>Næste trin

Lær mere om PowerShell scripting ved at læse [Scripting med Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) og se andre Azure PowerShell-scripts [Script Center](https://azure.microsoft.com/documentation/scripts/).
