<properties
    pageTitle="Python internettet og arbejder roller med Visual Studio | Microsoft Azure"
    description="Oversigt over brug af Python Tools til Visual Studio til at oprette Azure-skytjenester, herunder web roller og arbejder roller."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/03/2016"
    ms.author="adegeo"/>


# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Python internettet og arbejder roller med Python Tools til Visual Studio

Denne artikel indeholder en oversigt over brug af Python-internettet og arbejder roller ved hjælp af [Python værktøjer til Visual Studio][]. Du lære, hvordan du kan bruge Visual Studio til at oprette og installere en grundlæggende skybaseret tjeneste, der bruger Python.

## <a name="prerequisites"></a>Forudsætninger

 - Visual Studio 2013 eller 2015
 - [Python Tools til Visual Studio][] (PTVS)
 - [Azure SDK værktøjer til VS 2013][] eller [Azure SDK værktøjer for VS 2015][]
 - [Python 2.7 32-bit][] eller [Python 3.5 32-bit][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Hvad er Python internettet og arbejder roller?

Azure leverer tre beregne modeller til at køre programmer: [Web Apps-funktion i Azure App Service][execution model-web sites], [virtuelle Azure-computere][execution model-vms], og [Azure Cloud Services][execution model-cloud services]. Alle tre modeller understøtter Python. Cloud Services, som omfatter internettet og arbejder roller, giver *Platform som en tjeneste (PaaS)*. I en skybaseret tjeneste giver en web rolle en dedikeret Internet Information Services (IIS) webserver til host front end-programmer, mens en kollega rolle kan køre asynkron, længerevarende eller permanent opgaver uafhængigt af brugerinput eller input.

Du kan finde flere oplysninger, se [Hvad er en skybaseret tjeneste?].

> [AZURE.NOTE]*Gennemgang af til at oprette et enkelt websted?*
Hvis din scenario omfatter kun en enkelt websted front end, kan du overveje at bruge funktionen lette Web Apps i Azure App-tjeneste. Du kan nemt opgradere til en skybaseret tjeneste, som webstedet vokser og ændre dine behov. Se <a href="/develop/python/">Python Developer Center</a> til artikler, der dækker udvikling af funktionen Web Apps i Azure App Service.
<br />


## <a name="project-creation"></a>Projektoprettelse

I Visual Studio, kan du vælge **Azure skybaseret tjeneste** i dialogboksen **Nyt projekt** under **Python**.

![Dialogboksen nyt projekt](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Du kan oprette nye internettet og arbejder roller i guiden Azure skybaseret tjeneste.

![Dialogboksen Azure skybaseret tjeneste](./media/cloud-services-python-ptvs/new-service-wizard.png)

Skabelonen arbejder rolle leveres med dele af et kode til at oprette forbindelse til en Azure-lager-konto eller Azure Service Bus.

![Skybaseret tjeneste løsning](./media/cloud-services-python-ptvs/worker.png)

Du kan tilføje web eller arbejder roller til en eksisterende skybaseret tjeneste når som helst.  Du kan vælge at tilføje eksisterende projekter i din løsning eller oprette nye.

![Føje rolle kommando](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Skybaseret tjeneste kan indeholde roller, der er implementeret på forskellige sprog.  For eksempel kan du få en Python web rolle, der er implementeret ved hjælp af Django, med Python eller med C# arbejder roller.  Du kan nemt kommunikere mellem dine roller, der bruger Service Bus køer eller lagerplads køer.

## <a name="install-python-on-the-cloud-service"></a>Installere Python på skytjenesten

>[AZURE.WARNING] Konfiguration af scripts, der installeres med Visual Studio (på det tidspunkt, der sidst blev opdateret i denne artikel) virker ikke. I dette afsnit beskrives en løsning.

Det primære problem med installationsscripts er, at de ikke installerer python. Først skal definere to [Start opgaver](cloud-services-startup-tasks.md) i filen [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) . Den første opgave (**PrepPython.ps1**) downloader og installerer Python runtime. Den anden opgave (**PipInstaller.ps1**) kører pip for at installere en hvilken som helst afhængigheder, du skal muligvis.

De nedenfor scripts blev skrevet målretning af Python 3.5. Hvis du vil bruge versionen 2.x af python, angive **PYTHON2** variable filen til **på** til de to Start opgaver og runtime opgaven: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
    
  </Task>

</Startup>
```

**PYTHON2** og **PYPATH** variablerne skal føjes til arbejder Start opgaven. Variablen **PYPATH** bruges kun, hvis variablen **PYTHON2** er indstillet til **til**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Eksempel på ServiceDefinition.csdef

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Dernæst skal du oprette **PrepPython.ps1** og **PipInstaller.ps1** filer i den **. / placering** mappe med din rolle.

#### <a name="preppythonps1"></a>PrepPython.ps1

Dette script installerer python. Hvis variablen **PYTHON2** forsøgsarealer er indstillet til **på** og derefter Python 2.7 skal installeres, ellers Python 3.5 skal installeres.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }
        
        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1

Dette script kalder op pip og installerer alle afhængighederne i filen **requirements.txt** . Hvis variablen **PYTHON2** forsøgsarealer er indstillet til **på** og derefter Python 2.7 der skal bruges, ellers Python 3.5 der skal bruges.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Ændre LaunchWorker.ps1

>[AZURE.NOTE] **LauncherWorker.ps1** fil er tale om et projekt **arbejder rolle** kræves for at udføre filen Start. Start filen er i et projekt **web rolle** i stedet defineret i egenskaberne for projektet.

**Bin\LaunchWorker.ps1** blev oprettet for at gøre en masse forberedelse, men den er det virkelig virker ikke. Erstat indholdet i den pågældende fil med følgende script.

Dette script kalder **worker.py** filen fra projektet python. Hvis variablen **PYTHON2** forsøgsarealer er indstillet til **på** og derefter Python 2.7 der skal bruges, ellers Python 3.5 der skal bruges.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>PS.cmd

Visual Studio skabeloner der skulle have oprettet en **ps.cmd** fil i den **. / placering** mappe. Dette shell script kalder ud over PowerShell slikpapir-scripts og giver logføring baseret på navnet på den PowerShell slikpapir kaldet. Hvis denne fil ikke blev oprettet, er her, hvad skal være i den. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Kør lokalt

Hvis du angiver projektet skybaseret tjeneste som projektets start, og tryk på F5, kører skytjenesten i den lokale Azure emulator.

Selvom PTVS understøtter fungerer starte emulatoren, fejlfinding (for eksempel pausepunkter) ikke.

For at fejlfinde dine internettet og arbejder roller, kan du angive det rolle projekt som projektets start og fejlfinding, i stedet.  Du kan også angive flere Start projekter.  Højreklik på løsningen, og vælg derefter **Angive start-projekter**.

![Løsning startegenskaberne Project](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publicere til Azure

Hvis du vil publicere, skal du højreklikke på skyen service projektet i løsningen, og vælg derefter **Publicer**.

![Microsoft Azure publicere logon](./media/cloud-services-python-ptvs/publish-sign-in.png)

Følg anvisningerne i guiden. Hvis du vil, skal du aktivere Fjernskrivebord. Fjernskrivebord er nyttigt, når du har brug at foretage fejlfinding af noget.

Når du er færdig konfiguration af indstillinger, klik på **Publicer**.

Nogle status vises i outputvinduet, og derefter får du vist vinduet Microsoft Azure aktivitetslog.

![Vinduet Microsoft Azure aktivitet Log](./media/cloud-services-python-ptvs/publish-activity-log.png)

Installation kan tage flere minutter at gennemføre, og derefter dine web og/eller arbejder roller skal køre på Azure!

### <a name="investigate-logs"></a>Undersøge logfiler

Når virtual machine til skyen tjenesten starter, og Python er blevet installeret, kan du se logfiler til at finde de manglende meddelelser. Disse logfiler er placeret i den **C:\Resources\Directory\\{rollen} \LogFiles** mappe. **PrepPython.err.txt** , har mindst én fejl i den fra, når scriptet forsøger at finde om Python er installeret og **PipInstaller.err.txt** kan klager over en forældet version af pip.

## <a name="next-steps"></a>Næste trin

Finde mere detaljerede oplysninger om at arbejde med internettet og arbejder roller i Python Tools til Visual Studio, i dokumentationen til PTVS:

- [Skybaseret tjenesteprojekter][]

Du kan finde flere oplysninger om brug af Azure tjenester fra din internettet og arbejder roller, som bruger Azure-lager eller Service Bus, i følgende artikler.

- [BLOB-tjenesten][]
- [Tabeltjenesten][]
- [Kø Service][]
- [Service Bus køer][]
- [Tjenesten Bus emner][]


<!--Link references-->

[Hvad er en skybaseret tjeneste?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[BLOB-tjenesten]: ../storage/storage-python-how-to-use-blob-storage.md
[Kø Service]: ../storage/storage-python-how-to-use-queue-storage.md
[Tabeltjenesten]: ../storage/storage-python-how-to-use-table-storage.md
[Service Bus køer]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Tjenesten Bus emner]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools til Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Skybaseret tjenesteprojekter]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure SDK værktøjer til VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK værktøjer til VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/
