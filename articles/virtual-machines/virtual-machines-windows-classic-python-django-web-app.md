<properties
    pageTitle="Python web app med Django | Microsoft Azure"
    description="Dette selvstudium lærer du at være vært for en Django-baseret websted på Azure ved hjælp af en Windows Server 2012 R2 Datacenter virtuel maskine, ved hjælp af den klassiske implementeringsmodel."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Django Hej verden webprogram på en Windows Server VM

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Dette selvstudium beskrives, hvordan du vært for et websted Django-baseret på Microsoft Azure ved hjælp af en Windows Server virtuel maskine. Dette selvstudium antages det, at du ikke har nogen foregående erfaring med Azure. Når du har fuldført dette selvstudium, vil du have et Django-baseret program op og kører i skyen.

Skal du lære, hvordan du:

* Konfigurere en Azure virtuelt vært Django. Mens dette selvstudium forklares det, hvordan du gør dette under Windows Server, kan den samme også udføres med en Linux VM Azure som vært.
* Oprette et nyt Django program fra Windows.

Ved at følge dette selvstudium, kan du oprette et simpelt Hej verden webprogram. Programmet skal placeres i en Azure virtuelt.

Et skærmbillede af det færdige program vises næste.

![Et browservindue, der viser siden Hej verden på Azure][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Oprette og konfigurere en Azure virtuelt vært Django

1. Følg vejledningen angivne [her](virtual-machines-windows-classic-tutorial.md) til at oprette en Azure virtuelt for den stokastiske variabel Windows Server 2012 R2 Datacenter.

1. Fortæl Azure til at dirigere port 80 trafik fra internettet til port 80 på den virtuelle maskine:
 - Gå til din nyoprettede virtuelle maskine i portalen Azure klassisk, og klik på fanen **SLUTPUNKTER** .
 - Klik på knappen **Tilføj** i bunden af skærmen.
    ![tilføje slutpunkt](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Åbn af **TCP-** protokollen er **offentlige PORT 80** som **privat PORT 80**.
![][port80]
1. Klik på **Opret forbindelse** for at bruge **Fjernskrivebord** til at logge eksternt i det nyoprettede Azure virtuelt fra fanen **DASHBOARD** .  

**Vigtig bemærkning:** Alle vejledningen nedenfor antages det, du har logget på den virtuelle maskine korrekt og udsender kommandoer der i stedet for din lokale computer.

## <a id="setup"> </a>Installerer Python, Django, WFastCGI

**Note:** For at hente ved hjælp af Internet Explorer, du skal muligvis konfigurere indstillinger for IE ESC (Start/Administrative funktioner/Server Manager/lokal Server, klik derefter på **IE Enhanced Security Configuration**, slået fra).

1. Du kan installere den nyeste Python 2.7 eller 3.4 fra [python.org][].
1. Installere de wfastcgi og django pakker ved hjælp af pip.

    Brug følgende kommando for Python 2.7.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Brug følgende kommando for Python 3.4.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Installation af IIS med FastCGI

1. Installere IIS med FastCGI support.  Det kan tage flere minutter at udføre.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Oprette et nyt Django program

1.  Skriv følgende kommando for at oprette et nyt projekt i Django fra *C:\inetpub\wwwroot*:

    Brug følgende kommando for Python 2.7.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Brug følgende kommando for Python 3.4.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![Resultatet af kommandoen ny AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  Kommandoen **django administrator** genererer en grundlæggende struktur for Django-baserede websteder:

  -   **helloworld\manage.py** hjælper dig med at starte vært og standse vært for dit Django-baseret websted
  -   **helloworld\helloworld\settings.py** indeholder Django indstillinger for dit program.
  -   **helloworld\helloworld\urls.py** indeholder koden tilknytning mellem hvert URL-adresse og dens visning.

1.  Oprette en ny fil med navnet **views.py** i mappen *C:\inetpub\wwwroot\helloworld\helloworld* . Dette skal indeholde den visning, der gengiver siden "Hej verden". Start din editor, og Angiv følgende oplysninger:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Erstat indholdet af filen urls.py med følgende.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Konfiguration af IIS

1. Låse op sektionen programmer i den globale applicationhost.config.  Dette vil aktivere brugen af python handler i din web.config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Aktivere WFastCGI.  Dette vil føje et program til den globale applicationhost.config, som henviser til din eksekverbare Python fortolker og scriptet wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Oprette en web.config-fil i *C:\inetpub\wwwroot\helloworld*.  Værdien af den `scriptProcessor` attributten skal svare til output fra det forrige trin.  Se siden for [wfastcgi][] på pypi mere om indstillinger for wfastcgi.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Opdatere placeringen af IIS standardwebstedet til at pege på mappen django projekt.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Til sidst skal indlæse websiden i din browser.

![Et browservindue, der viser siden Hej verden på Azure][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Lukke din Azure virtuelle maskine

Når du er færdig med dette selvstudium, lukke og/eller fjerne din nyoprettede Azure virtuelle maskine for at frigøre ressourcer til andre selvstudier og undgå at skulle Azure brugen betale.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
