<properties
    pageTitle="Oprettelse af online med Django i Azure"
    description="Et selvstudium, der introducerer dig til at køre en Python WebApp i Azure App Service Web Apps."
    services="app-service\web"
    documentationCenter="python"
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article" 
    ms.date="02/19/2016"
    ms.author="huvalo"/>


# <a name="creating-web-apps-with-django-in-azure"></a>Oprettelse af online med Django i Azure

Dette selvstudium beskriver, hvordan du kommer i gang kører Python på [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Web Apps giver begrænset gratis vært og hurtig installation og du kan bruge Python! Efterhånden som din app vokser, kan du skifte til betalt vært, og du kan også integrere med alle de andre Azure tjenester.

Du opretter et program ved hjælp af Django web framework (se alternative versioner af dette selvstudium for [ [flasker](web-sites-python-create-deploy-bottle-app.md), og der](web-sites-python-create-deploy-flask-app.md) ). Du kan oprette WebApp fra Azure Marketplace, konfigurere ciffer installation og klone lager lokalt. Derefter vil du køre programmet lokalt, foretag ændringer, acceptere og overføre dem til Azure. Selvstudiet viser, hvordan du gør dette fra Windows eller Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.


## <a name="prerequisites"></a>Forudsætninger

- Windows, Mac eller Linux
- Python 2.7 eller 3.4
- setuptools, pip, virtualenv (kun Python 2.7)
- Ciffer
- [Python Tools til Visual Studio][] (PTVS) - Bemærk: Dette er valgfrit

**Bemærk**: TFS udgivelse understøttes ikke i øjeblikket for Python projekter.

### <a name="windows"></a>Windows

Hvis du ikke allerede har Python 2.7 eller 3,4 installerede (32-bit), anbefaler vi installation af [Azure SDK for Python 2.7] eller [Azure SDK for Python 3.4] ved hjælp af Web Platform Installer. Dette installeres 32-bit versionen af Python, setuptools, pip, virtualenv osv (32-bit Python er, hvad der er installeret på Azure host-computere). Du kan også åbne Python [python.org].

For ciffer anbefaler vi [Ciffer til Windows] eller [GitHub til Windows]. Hvis du bruger Visual Studio, kan du bruge den integrerede ciffer understøttelse.

Vi anbefaler også installerer [Python værktøjer 2.2 til Visual Studio]. Dette er valgfrit, men hvis du har [Visual Studio], herunder gratis Visual Studio Community 2013 eller Visual Studio Express 2013 til internettet, derefter dette giver dig en god IDE Python.

### <a name="maclinux"></a>Mac/Linux

Du skal have Python og ciffer, der allerede er installeret, men Sørg for, at du har Python 2.7 eller 3.4.


## <a name="web-app-creation-on-portal"></a>Oprettelse af Web App på portalen

Det første trin til oprettelse af din app er at oprette online via [Azure-portalen](https://portal.azure.com).

1. Log på portalen Azure, og klik på knappen **Ny** i det nederste venstre hjørne.
3. Skriv "python" i søgefeltet.
4. Vælg **Django** (udgivet af PTVS) i søgeresultaterne, og derefter klikke på **Opret**.
5. Konfigurere den nye Django-app, som opretter en ny App Service-plan og en ny ressourcegruppe til den. Klik derefter på **Opret**.
6. Konfigurere ciffer udgivelse for din nyoprettede online ved at følge vejledningen på [Lokale ciffer installation til Azure App-tjenesten](app-service-deploy-local-git.md).

## <a name="application-overview"></a>Oversigt over anvendelse

### <a name="git-repository-contents"></a>Ciffer lager indhold

Her er en oversigt over de filer, finder du i den indledende ciffer lager, som vi vil klone i næste afsnit.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Primære kilder til programmet. Består af 3 sider (indeks, om kontakten) med en masterlayoutet. Statisk indhold og scripts, som omfatter bootstrap, jquery, modernizr og svar.

    \manage.py

Lokal administration og udvikling server support. Brug dette til at køre programmet lokalt, skal du synkronisere den database, osv.

    \db.sqlite3

Standarddatabase. Indeholder de nødvendige tabeller til programmet til at køre, men ikke indeholder alle brugere (synkroniseres database for at oprette en bruger).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Project-filer til brug sammen med [Python Tools til Visual Studio].

    \ptvs_virtualenv_proxy.py

IIS-proxy for virtuelle miljøer og PTVS remote fejlfinding support.

    \requirements.txt

Eksterne pakker, der skal bruges af dette program. Scriptet installation vil pip installere de pakker, der er angivet i filen.

    \web.2.7.config
    \web.3.4.config

IIS konfigurationsfiler. Scriptet installation kan bruge den relevante web.x.y.config og kopiere den som web.config.

### <a name="optional-files---customizing-deployment"></a>Valgfri filer - tilpasning installation

[AZURE.INCLUDE [web-sites-python-django-customizing-deployment](../../includes/web-sites-python-django-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Valgfri filer - Python runtime

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Flere filer på server

Nogle filer findes på serveren, men det er ikke føjet til ciffer lager. Disse er oprettet af scriptet installation.

    \web.config

IIS konfigurationsfil. Oprettet ud fra web.x.y.config på hver installation.

    \env\

Python virtuelt miljø. Oprettes under installationen, hvis et kompatible virtuelt miljø ikke allerede findes på WebApp. Pakker i requirements.txt er pip installeret, men pip springe installationen, hvis der allerede er installeret pakkerne.

I næste 3 afsnit beskrives, hvordan du kan fortsætte med web app udvikling under 3 forskellige miljøer:

- Windows, med Python Tools til Visual Studio
- Windows, med kommandolinjen
- Mac/Linux, med kommandolinjen


## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Web app development - Windows - Python Tools til Visual Studio

### <a name="clone-the-repository"></a>Klone lageret

Først skal klone lager ved hjælp af på URL-adressen på Azure-portalen. Se [Lokale ciffer installation til Azure App Service](app-service-deploy-local-git.md)kan finde flere oplysninger.

Åbn den løsningsfil (.sln), der er inkluderet i roden af lageret.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### <a name="create-virtual-environment"></a>Oprette virtuelle miljø

Nu skal vi oprette et virtuelt miljø til lokale udvikling. Højreklik på **Python miljøer** Vælg **Tilføj virtuelt miljø …**.

- Sørg for, at navnet på miljøet er `env`.

- Vælg den grundlæggende fortolker. Sørg for at bruge den samme version af Python, der er valgt til din online (i runtime.txt eller bladet **Programindstillinger** for din online i portalen Azure).

- Kontrollér, at muligheden for at hente og installere pakker er markeret.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Klik på **Opret**. Dette vil oprette det virtuelle miljø, og Installer afhængigheder, der er angivet i requirements.txt.

### <a name="create-a-superuser"></a>Oprette en superbruger

Den database, der følger med programmet har ikke en hvilken som helst superbruger, der er defineret. For at anvende funktionerne logon i programmet eller grænsefladen Django administrator (Hvis du beslutter dig for at aktivere den), skal du oprette en superbruger.

Køre dette fra kommandolinjen fra projektmappen:

    env\scripts\python manage.py createsuperuser

Følg vejledningen for at angive brugernavn, din adgangskode, osv.

### <a name="run-using-development-server"></a>Køre ved hjælp af udviklingsserver

Tryk på F5 for at starte fejlfindingen og webbrowseren åbnes automatisk til den side, der kører lokalt.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Du kan angive pausepunkter i kilderne, bruge se windows osv. Se [Python Tools til Visual Studio dokumentation] kan finde flere oplysninger om de forskellige funktioner.

### <a name="make-changes"></a>Foretage ændringer

Nu kan du eksperimentere ved at foretage ændringer i programmet kilder og/eller skabeloner.

Når du har kontrolleret dine ændringer, du acceptere dem til ciffer lager:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### <a name="install-more-packages"></a>Installere flere pakker

Programmet kan have afhængigheder ud over Python og Django.

Du kan installere flere pakker ved hjælp af pip. Højreklik på det virtuelle miljø for at installere en pakke, og vælg **Installere Python pakke**.

Hvis du vil installere Azure SDK til Python, som giver dig adgang til Azure-lager, service bus og andre Azure tjenester, f.eks `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Højreklik på det virtuelle miljø, og vælg **Generer requirements.txt** opdatere requirements.txt.

Aktivere ændringerne til requirements.txt til ciffer lager.

### <a name="deploy-to-azure"></a>Installere på Azure

Hvis du vil udløse en installation, klik på **Synkroniser** eller **Push**. Synkronisering sker både en opslagsnål og en hente.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

Den første installation vil gå et stykke tid, som det opretter et virtuelt miljø, Installer-pakker osv.

Visual Studio få ikke vist status for installationen. Hvis du vil gennemse output, skal du se afsnittet om [fejlfinding - installation](#troubleshooting-deployment).

Gå til Azure URL-adressen til at få vist ændringerne.


## <a name="web-app-development---windows---command-line"></a>Web app development - Windows - kommandolinjen.

### <a name="clone-the-repository"></a>Klone lageret

Først skal klone lager ved hjælp af på URL-adressen på Azure-portalen, og Tilføj Azure lager som en remote. Se [Lokale ciffer installation til Azure App Service](app-service-deploy-local-git.md)kan finde flere oplysninger.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### <a name="create-virtual-environment"></a>Oprette virtuelle miljø

Vi vil oprette et nyt virtuelt miljø til udviklingsformål (Undlad at føje det til lageret). Virtuelle miljøer i Python er ikke flytbar, så alle arbejder på programmet udvikler kan oprette deres egne lokalt.

Sørg for at bruge den samme version af Python, der er valgt til din online (i runtime.txt eller bladet programindstillinger for din online i portalen Azure).

For Python 2.7:

    c:\python27\python.exe -m virtualenv env

For Python 3.4:

    c:\python34\python.exe -m venv env

Installere eventuelle eksterne pakker, som kræves af dit program. Du kan bruge filen requirements.txt i roden af lageret for at installere pakkerne i din virtuelt miljø:

    env\scripts\pip install -r requirements.txt

### <a name="create-a-superuser"></a>Oprette en superbruger

Den database, der følger med programmet har ikke en hvilken som helst superbruger, der er defineret. For at anvende funktionerne logon i programmet eller grænsefladen Django administrator (Hvis du beslutter dig for at aktivere den), skal du oprette en superbruger.

Køre dette fra kommandolinjen fra projektmappen:

    env\scripts\python manage.py createsuperuser

Følg vejledningen for at angive brugernavn, din adgangskode, osv.

### <a name="run-using-development-server"></a>Køre ved hjælp af udviklingsserver

Du kan starte programmet under en udviklingsserver med følgende kommando:

    env\scripts\python manage.py runserver

Konsollen vises URL-adressen og port serveren lytter til:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Åbn derefter din webbrowser til URL-adressen.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### <a name="make-changes"></a>Foretage ændringer

Nu kan du eksperimentere ved at foretage ændringer i programmet kilder og/eller skabeloner.

Når du har kontrolleret dine ændringer, du acceptere dem til ciffer lager:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installere flere pakker

Programmet kan have afhængigheder ud over Python og Django.

Du kan installere flere pakker ved hjælp af pip. Skriv for at installere Azure SDK til Python, som giver dig adgang til Azure-lager, service bus og andre Azure tjenester, f.eks.:

    env\scripts\pip install azure

Sørg for at opdatere requirements.txt:

    env\scripts\pip freeze > requirements.txt

Foretag ændringerne:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Installere på Azure

Hvis du vil udløse en installation, du overføre ændringerne til Azure:

    git push azure master

Output fra scriptet installation, herunder virtuelt miljø oprettelse, installation af pakker, oprettelse af web.config vises.

Gå til Azure URL-adressen til at få vist ændringerne.


## <a name="web-app-development---maclinux---command-line"></a>Web app development Mac/Linux - kommandolinjen.

### <a name="clone-the-repository"></a>Klone lageret

Først skal klone lager ved hjælp af på URL-adressen på Azure-portalen, og Tilføj Azure lager som en remote. Se [Lokale ciffer installation til Azure App Service](app-service-deploy-local-git.md)kan finde flere oplysninger.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### <a name="create-virtual-environment"></a>Oprette virtuelle miljø

Vi vil oprette et nyt virtuelt miljø til udviklingsformål (Undlad at føje det til lageret). Virtuelle miljøer i Python er ikke flytbar, så alle arbejder på programmet udvikler kan oprette deres egne lokalt.

Sørg for at bruge den samme version af Python, der er valgt til din online (i runtime.txt eller bladet programindstillinger for din online i portalen Azure).

For Python 2.7:

    python -m virtualenv env

For Python 3.4:

    python -m venv env

eller

    pyvenv env

Installere eventuelle eksterne pakker, som kræves af dit program. Du kan bruge filen requirements.txt i roden af lageret for at installere pakkerne i din virtuelt miljø:

    env/bin/pip install -r requirements.txt

### <a name="create-a-superuser"></a>Oprette en superbruger

Den database, der følger med programmet har ikke en hvilken som helst superbruger, der er defineret. For at anvende funktionerne logon i programmet eller grænsefladen Django administrator (Hvis du beslutter dig for at aktivere den), skal du oprette en superbruger.

Køre dette fra kommandolinjen fra projektmappen:

    env/bin/python manage.py createsuperuser

Følg vejledningen for at angive brugernavn, din adgangskode, osv.

### <a name="run-using-development-server"></a>Køre ved hjælp af udviklingsserver

Du kan starte programmet under en udviklingsserver med følgende kommando:

    env/bin/python manage.py runserver

Konsollen vises URL-adressen og port serveren lytter til:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Åbn derefter din webbrowser til URL-adressen.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### <a name="make-changes"></a>Foretage ændringer

Nu kan du eksperimentere ved at foretage ændringer i programmet kilder og/eller skabeloner.

Når du har kontrolleret dine ændringer, du acceptere dem til ciffer lager:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installere flere pakker

Programmet kan have afhængigheder ud over Python og Django.

Du kan installere flere pakker ved hjælp af pip. Skriv for at installere Azure SDK til Python, som giver dig adgang til Azure-lager, service bus og andre Azure tjenester, f.eks.:

    env/bin/pip install azure

Sørg for at opdatere requirements.txt:

    env/bin/pip freeze > requirements.txt

Foretag ændringerne:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Installere på Azure

Hvis du vil udløse en installation, du overføre ændringerne til Azure:

    git push azure master

Output fra scriptet installation, herunder virtuelt miljø oprettelse, installation af pakker, oprettelse af web.config vises.

Gå til Azure URL-adressen til at få vist ændringerne.


## <a name="troubleshooting---package-installation"></a>Fejlfinding - pakkeinstallation

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Fejlfinding – virtuelt miljø

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## <a name="troubleshooting---static-files"></a>Fejlfinding - statiske filer

Django har begrebet indsamling af statiske filer. Det tager alle statiske filer fra den oprindelige placering og kopierer dem til en enkelt mappe. Til dette program, de er kopieret til `/static`.

Dette sker, fordi statiske filer kan komme fra forskellige Django 'apps'. De statiske filer fra Django administrator grænseflader kan for eksempel findes i en Django bibliotek undermappe i det virtuelle miljø. Statiske filer, der er defineret af dette program er placeret i `/app/static`. Når du bruger flere Django 'apps', har du statiske filer, der findes flere steder.

Når du kører programmet i fejlsikret tilstand, fungerer programmet statisk filerne fra den oprindelige placering.

Når du kører programmet i release tilstand, programmet fungerer **ikke** fungere statiske filer. Det er ansvarlig for webserveren at kunne levere filerne. Til dette program, fungerer IIS statisk filerne fra `/static`.

Samling af statiske filer sker automatisk som en del af scriptet installation, når du fjerner markeringen tidligere indsamles filer. Det betyder samlingen opstår på hver installation, langsommere installation lidt, men det sikrer, at forældede filer ikke vil være tilgængeligt, undgår et potentielt sikkerhedsproblem.

Hvis du vil springe over samling af statiske filer til dit Django program:

    \.skipDjango

Derefter skal du gøre samlingen manuelt på din lokale computer:

    env\scripts\python manage.py collectstatic

Fjern den `\static` mappe fra `.gitignore` og tildele den til ciffer lager.


## <a name="troubleshooting---settings"></a>Fejlfinding - indstillinger

Forskellige indstillinger for programmet kan ændres på `DjangoWebProject/settings.py`.

Udvikler fordel er fejlsikret tilstand aktiveret. Et godt side effekten af at, som er, du vil kunne se billeder og andre statisk indhold, når du kører lokalt, uden at indsamle statiske filer.

Sådan deaktiveres fejlsikret tilstand:

    DEBUG = False

Når fejlfinding er deaktiveret, værdien for `ALLOWED_HOSTS` skal opdateres, hvis du vil medtage Azure værtsnavnet. Eksempel:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

eller aktivere:

    ALLOWED_HOSTS = (
        '*',
    )

I øvelsen vil du måske gøre noget mere komplekse til at behandle Skift mellem fejlfinding og slip-tilstand, og at få værtsnavnet.

Du kan angive miljøvariabler via Azure administrationsportalen **KONFIGURER** side i sektionen **Indstillinger** .  Det kan være nyttige til at angive værdier, der ikke kan skal vises i kilderne (strenge, adgangskoder osv.), eller som du vil angive anderledes mellem Azure og din lokale computer. I `settings.py`, kan du forespørge på miljøvariabler ved hjælp af `os.getenv`.


## <a name="using-a-database"></a>Ved hjælp af en Database

Den database, der følger med programmet er en sqlite database. Dette er en praktisk og nyttige standarddatabase skal bruges til udvikling, som kræver næsten ingen installation. Databasen er gemt i filen db.sqlite3 i projektmappen.

Azure leverer databasetjenester, som er nem at bruge fra et Django program. Selvstudier til brug af [SQL-Database] og [MySQL] fra et Django program vise trinnene nødvendigt at oprette tjenesten til databasen, ændre databaseindstillingerne for i `DjangoWebProject/settings.py`, og de biblioteker, der kræves for at installere.

Naturligvis, hvis du foretrækker at administrere din egen databaseservere, kan du gøre det ved hjælp af Windows eller Linux virtuelle maskiner, der kører på Azure.


## <a name="django-admin-interface"></a>Django administrator grænseflade

Når du begynder at bygge din modeller, skal du til at udfylde databasen med nogle data. En nem måde at tilføje og redigere indhold interaktivt er at bruge grænsefladen Django administration.

Koden for grænsefladen administrator er kommenterede ud i programmet kilder, men det er tydeligt markeret, så du nemt kan aktivere det (Søg efter "admin").

Når den er aktiveret, synkronisere databasen, køre programmet, og gå til `/admin`.


## <a name="next-steps"></a>Næste trin

Følg disse links for at lære mere om Django og Python værktøjer til Visual Studio:

- [Django dokumentation]
- [Python Tools til Visual Studio dokumentation]

Oplysninger om brug af SQL-Database og MySQL:

- [Django og MySQL på Azure med Python Tools til Visual Studio]
- [Django og SQL-Database på Azure med Python Tools til Visual Studio]

Du kan finde yderligere oplysninger finder [Python Developer Center](/develop/python/).


## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Django og MySQL på Azure med Python Tools til Visual Studio]: web-sites-python-ptvs-django-mysql.md
[Django og SQL-Database på Azure med Python Tools til Visual Studio]: web-sites-python-ptvs-django-sql.md
[SQL-Database]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[Python.org]: http://www.python.org/
[Ciffer til Windows]: http://msysgit.github.io/
[GitHub til Windows]: https://windows.github.com/
[Python Tools til Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 til Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Python Tools til Visual Studio dokumentation]: http://aka.ms/ptvsdocs
[Django dokumentation]: https://www.djangoproject.com/
