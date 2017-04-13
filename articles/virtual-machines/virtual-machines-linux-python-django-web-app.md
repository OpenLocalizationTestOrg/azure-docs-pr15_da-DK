<properties 
    pageTitle="Python web app med Django på Linux | Microsoft Azure" 
    description="Lær at være vært for et Django-baserede webprogram på Azure ved hjælp af en Linux virtuel maskine." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Django Hej verden webprogram på en Linux VM

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

Dette selvstudium beskrives, hvordan du vært for et websted Django-baseret på Microsoft Azure ved hjælp af en Linux virtuel maskine. Dette selvstudium antages det, at du ikke har nogen foregående erfaring med Azure. Når denne vejledning, vil du have et Django-baseret program op og kører i skyen.

Skal du lære, hvordan du:

* Konfigurere en Azure virtuelt vært Django. Mens dette selvstudium forklares det, hvordan du gør dette under **Linux**, kan den samme også udføres med et Windows Server-VM Azure som vært. 
* Oprette et nyt Django program fra Linux.

Ved at følge dette selvstudium, kan du oprette et simpelt Hej verden webprogram. Programmet skal placeres i en Azure virtuelt.

Et skærmbillede af det færdige program er nedenfor:

![Et browservindue, der viser siden Hej verden på Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Oprette og konfigurere en Azure virtuelt vært Django

1. Følg vejledningen angivne [her](virtual-machines-linux-quick-create-portal.md) til at oprette en Azure virtuelt for den stokastiske variabel *Ubuntu Server 14.04 LTS* .  Hvis du foretrækker, kan du vælge godkendelse af adgangskode i stedet for SSH offentlig nøgle.

1. Redigere sikkerhedsgruppe netværk for at tillade indgående http-trafik til port 80 ved hjælp af vejledningen [her](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Som standard har din nye virtuelle maskine ikke et fuldt domænenavn (fulde Domænenavn).  Du kan oprette en ved at følge vejledningen [her](virtual-machines-linux-portal-create-fqdn.md).  Dette trin er valgfrit til at udføre dette selvstudium.

## <a id="setup"> </a>Konfiguration af udviklingsmiljøet

**Note:** Hvis du skal installere Python eller vil gerne bruge klient-biblioteker, skal du se [Installationsvejledningen til Python](../python-how-to-install.md).

Ubuntu Linux VM følger allerede med Python 2.7 allerede er installeret, men det har ikke Apache eller django, der er installeret.  Følg disse trin for at oprette forbindelse til din VM og installere Apache og django.

1.  Start et nyt **Terminal** -vindue.
    
1.  Skriv følgende kommando for at oprette forbindelse til Azure VM.  Hvis du ikke opretter en fulde Domænenavn, kan du oprette forbindelse ved hjælp af den offentlige IP-adresse, der vises i den virtuelle maskine oversigt i portalen Azure klassisk.

        $ ssh yourusername@yourVmUrl

1.  Skriv følgende kommandoer for at installere django:

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Skriv følgende kommando for at installere Apache med rest-wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Oprette et nyt Django program

1.  Åbn vinduet **Terminal** , du har brugt i forrige afsnit for at ssh til din VM.
    
1.  Skriv følgende kommandoer for at oprette et nyt Django projekt:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    Scriptet **django admin.py** genererer en grundlæggende struktur for Django-baserede websteder:
    -   **helloworld/Manage.py** hjælper dig med at starte vært og standse vært for dit Django-baseret websted
    -   **helloworld/helloworld/Settings.py** indeholder Django indstillinger for dit program.
    -   **helloworld/helloworld/URLs.py** indeholder koden tilknytning mellem hvert URL-adresse og dens visning.

1.  Oprette en ny fil med navnet **views.py** i mappen **/var/www/helloworld/helloworld** . Dette skal indeholde den visning, der gengiver siden "Hej verden". Start din editor, og Angiv følgende oplysninger:
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Erstat nu indholdet af filen **urls.py** med følgende:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>Konfiguration af Apache

1.  Oprette en Apache virtuelle værter konfiguration fil **/etc/apache2/sites-available/helloworld.conf**. Angiv oplysningerne til følgende, og Erstat *yourVmName* med det faktiske navn på den computer, du bruger (for eksempel *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Aktivere på webstedet med følgende kommando:

        $ sudo a2ensite helloworld

1.  Genstart Apache med følgende kommando:

        $ sudo service apache2 reload

1.  Til sidst skal indlæse websiden i browseren:

    ![Et browservindue, der viser siden Hej verden på Azure](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Lukke din Azure virtuelle maskine

Når du er færdig med dette selvstudium, lukning og/eller fjern din nyoprettede Azure virtuelle maskine at frigøre ressourcer til andre selvstudier og undgå at skulle Azure brugen betale.
