<properties
   pageTitle="Installere .NET på en skybaseret tjeneste rolle | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du manuelt installere .NET framework på skyen Service internettet og arbejder roller"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Installere .NET på en skybaseret tjeneste rolle 

I denne artikel beskrives, hvordan du installerer .NET framework på skyen Service Web og arbejder roller. Du kan bruge disse trin til at installere .NET 4.6.1 på Azure gæst OS familie 4. Se de seneste oplysninger om gæst OS udgivelser [Azure gæst OS versioner og SDK kompatibilitet Matrix](cloud-services-guestos-update-matrix.md).

Processen med at installere .NET på dine internettet og arbejder roller omfatter herunder .NET installationsprogrammet som en del af projektet skyen og start installationsprogrammet som en del af den rolle Start opgaver.  

## <a name="add-the-net-installer-to-your-project"></a>Føje .NET installationsprogrammet til projektet
- Hente den web installationsprogrammet til .NET framework, du vil installere
    - [.NET 4.6.1 web Installer](http://go.microsoft.com/fwlink/?LinkId=671729)
- For en Web rolle
  1. I **Solution Explorer**, under i **roller** i skyen service projektet Højreklik på dit rolle og markerer **Tilføj > ny mappe**. Oprette en mappe med navnet *placering*
  2. Højreklik på mappen **bin** og vælge **Tilføj > eksisterende element**. Vælg .NET installationsprogrammet og tildele den til mappen bin.
- For en kollega rolle
  1. Højreklik på dit rolle og markerer **Tilføj > eksisterende element**. Vælg .NET installationsprogrammet og tildele den til rollen. 

Filerne føjes denne måde, til mappen rolle indhold bliver automatisk føjet til skyen servicepakke og installeret på en ensartet placering på den virtuelle maskine. Gentag denne proces for alle internettet og arbejder roller i skybaseret tjeneste, så alle roller har en kopi af installer.

> [AZURE.NOTE] Du skal installere .NET 4.6.1 på din rolle skybaseret tjeneste, selvom dit program er beregnet til .NET 4.6. Azure gæst OS omfatter opdateringer [3098779](https://support.microsoft.com/kb/3098779) og [3097997](https://support.microsoft.com/kb/3097997). Installation af .NET 4.6 oven på disse opdateringer kan give problemer, når du kører din .NET-programmer, så du skal installere .NET 4.6.1 i stedet for .NET 4.6 direkte. Du kan finde flere oplysninger [KB 3118750](https://support.microsoft.com/kb/3118750).

![Rolle-indhold med installationsfiler][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definere Start opgaver for dine roller
Start opgaver kan bruges til at udføre handlinger, før en rolle starter. Installere .NET Framework som en del af opgaven Start sikrer, at, som er installeret før en af dine programkode køres. Finde flere oplysninger under Start opgaver: [Køre Start opgaver i Azure](cloud-services-startup-tasks.md). 

1. Tilføj følgende filen *ServiceDefinition.csdef* under noden **WebRole** eller **WorkerRole** for alle roller:
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    Ovenstående konfigurationen køre console kommandoen *install.cmd* med administratorrettigheder, så den kan installere .NET framework. Konfigurationen opretter også en LocalStorage med navnet *NETFXInstall*. Scriptet til Start Indstiller mappen temp for at bruge denne lokale lager ressource, så installationsprogrammet til .NET framework kan hentes og installeres fra denne ressource. Det er vigtigt at angive størrelsen på denne ressource til mindst 1024MB at sikre, at, som installeres korrekt. Finde flere oplysninger om Start opgaver: [almindelige skytjeneste Start opgaver](cloud-services-startup-tasks-common.md) 

2. Oprette en fil **install.cmd** og føje det til alle roller ved at højreklikke på rollen og vælge **Tilføj > eksisterende element...**. Så alle roller bør nu have .NET-installationsfil samt filen install.cmd.
    
    ![Rolle-indhold med alle filer][2]

    > [AZURE.NOTE] Brug en teksteditor som Notesblok til at oprette denne fil. Hvis du bruger Visual Studio til at oprette en tekstfil og derefter omdøbe den til 'cmd.' filen stadig kan indeholde et UTF-8 Byte rækkefølge mærke og kører den første linje i scriptet medfører en fejl. Hvis du skulle bruge Visual Studio til at oprette tilføje fil Forlad REM (bemærkning) til den første linje i filen, så det ignoreres, når kører. 

3. Tilføj følgende script i filen **install.cmd** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    Installer scriptet kontrollerer, om den angivne .NET framework version allerede er installeret på maskinen ved at forespørge registreringsdatabasen. Hvis .NET versionen ikke er installeret derefter .net Web Installer er startet. Hjælp til fejlfinding af eventuelle problemer, med logfører scriptet alle aktivitet til en fil med navnet *startuptasklog-(AKTUELDATOKLOKKESLÆT) .txt* gemt i *InstallLogs* lokale lager.

    > [AZURE.NOTE] Scriptet stadig viser, hvordan du installerer .NET 4.5.2 eller .NET 4.6 til løbende. Det er ikke nødvendigt at installere .NET 4.5.2 manuelt, som den findes allerede på Azure gæst OS. Du skal installere .NET 4.6.1 på grund af [KB 3118750](https://support.microsoft.com/kb/3118750)direkte i stedet for installation af .NET 4.6.
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Konfigurere diagnosticering for at overføre loggene Start opgave for at blob storage 
Du kan konfigurere Azure diagnosticering for at overføre en hvilken som helst logfiler, der genereres af scriptet til start eller .NET installationsprogrammet til blob-lager for at forenkle fejlfinding af problemer med at installere. Med denne metode kan du få vist loggene ved blot hente logfilerne fra blob-lager i stedet for at skulle Fjernskrivebord til rollen.

Til at konfigurere diagnosticering Åbn *diagnostics.wadcfgx* , og Tilføj følgende under noden **mapper** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Dette vil konfigurere azure diagnosticering for at overføre alle filer i biblioteket over *logfiler* under *NETFXInstall* ressourcen til diagnosticering lagerplads kontoen i objektbeholderen *netfx Installer* blob.

## <a name="deploying-your-service"></a>Implementering af din tjeneste 
Når du installerer din tjeneste kører Start opgaver og installere .NET framework, hvis den ikke allerede er installeret. Din rolle bliver i tilstanden optaget, mens, som er installeret og genstarter muligvis også, hvis installationen af framework kræver det. 

## <a name="additional-resources"></a>Yderligere ressourcer

- [Installere .NET Framework][]
- [Sådan: finde ud af, hvilke .NET Framework versioner er installeret][]
- [Fejlfinding i forbindelse med .NET Framework-installationer][]

[Sådan: finde ud af, hvilke .NET Framework versioner er installeret]: https://msdn.microsoft.com/library/hh925568.aspx
[Installere .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Fejlfinding i forbindelse med .NET Framework-installationer]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
