<properties
    pageTitle="PHP internettet og arbejder roller | Microsoft Azure"
    description="En vejledning til at oprette PHP internettet og arbejder roller i en Azure skybaseret tjeneste, og konfigurere PHP runtime."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Sådan oprettes PHP internettet og arbejder roller

## <a name="overview"></a>Oversigt

Denne vejledning viser dig, hvordan oprette PHP web eller arbejder roller i et miljø til udvikling af Windows, vælge en bestemt version af PHP mellem de tilgængelige "indbyggede" versioner, ændre PHP konfigurationen, aktivere filtypenavne og til sidst skal installeres til Azure. Det også beskrives det, hvordan du konfigurerer en web eller arbejder rolle for at bruge en PHP runtime (med tilpasset konfiguration og filtypenavne), du angiver.

## <a name="what-are-php-web-and-worker-roles"></a>Hvad er PHP internettet og arbejder roller?

Azure leverer tre beregne modeller til at køre programmer: Azure App Service, virtuelle Azure-computere og Azure Cloud Services. Alle tre modeller understøtter PHP. Cloud Services, som omfatter internettet og arbejder roller, indeholder *platform som en tjeneste (PaaS)*. I en skybaseret tjeneste indeholder en web rolle en dedikeret Internet Information Services (IIS) webserver til host front end-programmer. En kollega rolle kan køre asynkron, længerevarende eller permanent opgaver uafhængigt af brugerinput eller input.

Finde flere oplysninger om disse indstillinger, [beregne hosting indstillingerne i Azure](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Hente Azure SDK til PHP

[Azure SDK til PHP] består af flere komponenter. I denne artikel anvender to af dem: Azure PowerShell og Azure emulatorer. Disse to komponenter kan installeres via Microsoft Web Platform Installer. Se, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md)kan finde flere oplysninger.

## <a name="create-a-cloud-services-project"></a>Oprette et Cloud Services-projekt

Det første trin i at oprette en PHP web eller arbejder rolle er at oprette en Azure Service projekt. et Azure Service projekt fungerer som en logisk objektbeholder til internettet og arbejder roller, og den indeholder projektets [definitionen af tjenesten (.csdef)] og [tjenestekonfiguration (.cscfg)] filer.

For at oprette et nyt projekt Azure Service skal køre Azure PowerShell som administrator og udføre følgende kommando:

    PS C:\>New-AzureServiceProject myProject

Denne kommando opretter en ny mappe (`myProject`), kan du tilføje internettet og arbejder roller.

## <a name="add-php-web-or-worker-roles"></a>Tilføje PHP web eller arbejder roller

Hvis du vil føje en PHP web rolle til et projekt, skal du køre følgende kommando fra i projektets rodmappe:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Brug denne kommando til en kollega rolle:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] Den `roleName` parameter er valgfri. Hvis det er udeladt, der Rollenavnet genereres automatisk. Den første web rolle oprettet bliver `WebRole1`, bliver andet `WebRole2`, og så videre. Den første arbejder rolle oprettet bliver `WorkerRole1`, bliver andet `WorkerRole2`, og så videre.

## <a name="specify-the-built-in-php-version"></a>Angive den indbyggede PHP version

Når du tilføjer en PHP web eller arbejder rolle til et projekt, ændres projektets konfigurationsfiler, så PHP installeres på hver web eller arbejder forekomst af dit program, når den er installeret. Hvis du vil se versionen af PHP, som skal installeres som standard, skal du køre følgende kommando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Output fra kommandoen ovenfor ser nogenlunde sådan Hvad er vist nedenfor. I dette eksempel på `IsDefault` flag er indstillet til `true` til PHP 5.3.17, der angiver, at det vil være standardversionen PHP installeret.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Du kan angive PHP runtime-version til en af de PHP versioner, der vises. For at angive PHP versionen (for en rolle med navnet `roleName`) til 5.4.0, kan du bruge følgende kommando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Tilgængelige PHP versioner kan blive ændret i fremtiden.

## <a name="customize-the-built-in-php-runtime"></a>Tilpasse den indbyggede PHP runtime

Du har fuld kontrol over konfigurationen af PHP runtime, der er installeret, når du følger trinnene ovenfor, herunder ændring af `php.ini` indstillinger og aktivering af filtypenavne.

Hvis du vil tilpasse den indbyggede PHP runtime, skal du følge disse trin:

1. Tilføje en ny mappe med navnet `php`, til den `bin` mappe på din web rolle. Til en kollega rolle, kan du føje den til den rolle rodmappen.
2. I den `php` mappe, Opret en anden mappe med navnet `ext`. Placer `.dll` lokalnummer filer (f.eks., `php_mongo.dll`), som du vil aktivere i denne mappe.
3. Tilføje en `php.ini` filen til den `php` mappe. Aktivere en hvilken som helst brugerdefinerede udvidelser og angive alle PHP direktiver i filen. For eksempel, hvis du vil slå `display_errors` på og aktivere den `php_mongo.dll` udvidelse, indholdet af din `php.ini` fil ville være på følgende måde:

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Alle indstillinger, som du ikke eksplicit er angivet i den `php.ini` fil, du angiver er automatisk angives til standardværdierne. Men husk på, at du kan tilføje en komplet `php.ini` fil.

## <a name="use-your-own-php-runtime"></a>Bruge dit eget PHP runtime
I nogle tilfælde, i stedet for at vælge en indbygget PHP runtime og konfigurere det som beskrevet ovenfor, kan du angive dine egne PHP runtime. Du kan for eksempel bruge den samme PHP runtime i en web eller arbejder rolle, du bruger i dit udviklingsmiljø. Det gør det nemmere at sikre, at programmet ikke ændre funktionsmåden i dit produktionsmiljø.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurere en web rolle for at bruge dit eget PHP runtime

For at konfigurere en web rolle for at bruge en PHP runtime, du angiver, skal du følge disse trin:

1. Oprette et Azure Service projekt og tilføje en PHP web rolle, som beskrevet tidligere i dette emne.
2. Oprette en `php` mappe i den `bin` mappe, der er i din web rolle rodmappe, og derefter føje din PHP runtime (alle binære filer, konfigurationsfiler, undermapper, osv.) til den `php` mappe.
3. (VALGFRIT) Hvis din PHP runtime bruger [Microsoft-drivere til PHP til SQL Server][sqlsrv drivers], skal du konfigurere din web rolle for at installere [SQL Server Native Client 2012] [ sql native client] når den er klargjort. Tilføje [sqlncli.msi x64 installationsprogrammet] til at gøre dette, den `bin` mappe i din web rolle rodmappe. Startscriptet er beskrevet i næste trin køres automatisk installationsprogrammet, når rollen er klargjort. Hvis din PHP runtime ikke bruger Microsoft Drivers til PHP til SQL Server, kan du fjerne følgende linje fra scriptet vises i næste trin:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definere en Start-opgave, der konfigurerer [Internet Information Services (IIS)] [ iis.net] til at bruge din PHP runtime til at håndtere anmodninger om `.php` sider. For at gøre dette skal du åbne den `setup_web.cmd` fil (i den `bin` fil af din web rolle rodmappen) i et tekstredigeringsprogram og erstatte indholdet med følgende script:

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Føje din programfiler til din web rolle rodmappen. Dette er den webserver rodmappen.

6. Publicer dit program, som beskrevet i afsnittet [Publicer dit program](#how-to-publish-your-application) nedenfor.

> [AZURE.NOTE] Den `download.ps1` script (i den `bin` mappe med rollen web rodmappen) kan blive slettet, når du har fulgt trinnene beskrevet ovenfor til at bruge dit eget PHP runtime.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurere en kollega rolle for at bruge dit eget PHP runtime

For at konfigurere en kollega rolle for at bruge en PHP runtime, du angiver, skal du følge disse trin:

1. Oprette et Azure Service projekt og tilføje en PHP arbejder rolle, som beskrevet tidligere i dette emne.
2. Oprette en `php` mappe i rollen arbejder rodmappen, og derefter føje din PHP runtime (alle binære filer, konfigurationsfiler, undermapper, osv.) til den `php` mappe.
3. (VALGFRIT) Hvis din PHP runtime bruger [Microsoft drivere til PHP til SQL Server][sqlsrv drivers], skal du konfigurere din kollega rolle for at installere [SQL Server Native Client 2012] [ sql native client] når den er klargjort. Føje [sqlncli.msi x64 installationsprogrammet] til rollen arbejder rodmappen for at gøre dette. Startscriptet er beskrevet i næste trin køres automatisk installationsprogrammet, når rollen er klargjort. Hvis din PHP runtime ikke bruger Microsoft Drivers til PHP til SQL Server, kan du fjerne følgende linje fra scriptet vises i næste trin:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definere en Start-opgave, der tilføjer din `php.exe` eksekverbare til rollen arbejder miljøvariablen PATH når rollen er klargjort. For at gøre dette skal du åbne den `setup_worker.cmd` filer (i rollen arbejder rodmappe) i et tekstredigeringsprogram og erstatte indholdet med følgende script:

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Føje din programfiler til din kollega rolle rodmappen.

6. Publicer dit program, som beskrevet i afsnittet [Publicer dit program](#how-to-publish-your-application) nedenfor.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Køre programmet i Beregn og lager emulatorer

Azure-emulatorer giver et lokalt miljø, hvor du kan teste din Azure-program, inden du installerer det i skyen. Der er nogle forskelle mellem emulatorer og Azure miljø. Se [Brug af Azure lagerplads emulatoren til udvikling og test](./storage/storage-use-emulator.md)dette for bedre for at forstå.

Bemærk, at du skal have installeret lokalt for at bruge Beregn emulatoren PHP. Beregn emulatoren anvender din lokale PHP installation for at køre programmet.

Hvis du vil køre dit projekt i emulatorer, skal du udføre følgende kommando fra dit projekts rodmappe:

    PS C:\MyProject> Start-AzureEmulator

Du får vist output nogenlunde sådan ud:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Du kan se dit program, der kører i emulatoren ved at åbne en webbrowser og gå til den lokale adresse, der vises i outputtet (`http://127.0.0.1:81` i eksempel output ovenfor).

Du kan stoppe emulatorer ved at udføre denne kommando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicere dit program

Hvis du vil publicere dit program, skal du starte med at importere dine indstillinger for publicering ved hjælp af [Importér AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) cmdlet. Derefter kan du publicere dit program ved hjælp af [Publicer AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) cmdlet. Se, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md)for at få oplysninger om at logge på.

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [PHP Developer Center](/develop/php/).

[Azure SDK til PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definitionen af tjenesten (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[tjenestekonfiguration (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[SQLNCli.msi x64 installer]: http://go.microsoft.com/fwlink/?LinkID=239648
