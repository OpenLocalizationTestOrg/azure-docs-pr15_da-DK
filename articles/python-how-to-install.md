<properties
    pageTitle="Installere Python og SDK - Azure"
    description="Få mere at vide, hvordan du installerer Python og SDK til brug med Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>Installere Python og SDK

Python er nemt at installationen på Windows og er forudinstalleret på Mac, Linux og [Bash til Windows](https://msdn.microsoft.com/commandline/wsl/about). Denne vejledning vejleder dig gennem installation og at få computeren klar til brug med Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Hvad er i Python Azure SDK?

Azure SDK for Python indeholder komponenter, der gør det muligt at udvikle, installere og administrere Python programmer til Azure. Nærmere betegnet kan indeholder Azure SDK for Python følgende:

* **Administration af biblioteker**. Disse klassebiblioteker har en grænseflade, administrere Azure ressourcer, som lager konti, virtuelle computere.

* **Runtime biblioteker**. Disse klassebiblioteker giver en grænseflade for at få adgang til Azure funktioner, som lager og service bus.

## <a name="which-python-and-which-version-to-use"></a>Hvilke Python og hvilken version du vil bruge

Der er flere typer af Python tolke - eksempler kan nævnes:

* CPython - standard- og mest almindeligt anvendte Python fortolker
* PyPy - hurtige og kompatibel alternativ implementering til CPython
* IronPython - Python fortolker, der kører på .net/CLR
* Jython - Python fortolker, der kører på Java Virtual Machine

**CPython** v2.7 eller v3.3 + og PyPy 5.4.0 er testet og understøttes for Python Azure SDK.

## <a name="where-to-get-python"></a>Hvor du kan få Python?

Der er flere måder at få CPython:

* Direkte fra [www.python.org][]
* Fra et anerkendt distro som [www.continuum.io][], [www.enthought.com][] eller [www.activestate.com][]
* Opbygge fra kilde!

Medmindre du har et bestemt formål, anbefaler vi de første to indstillinger.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>SDK-Installation på Windows, Linux og MacOS (kun klientbiblioteker)

Hvis du allerede har Python installeret, kan du bruge pip for at installere en samlet af alle klient-bibliotekerne på din eksisterende Python 2.7 eller Python 3.3 + miljø. Dette vil hente pakkerne fra [Python pakke indeks][] (PyPI).

Du skal muligvis administratorrettigheder:

- Linux og MacOS, bruger den `sudo` kommandoen: `sudo pip install azure-mgmt-compute`.
- Windows: åbne din PowerShell/kommandoprompt som administrator

Du kan installere individuelt hvert bibliotek for hver Azure service:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Eksempel-pakker kan installeres ved hjælp af den `--pre` flag:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Du kan også installere et sæt af Azure biblioteker i en enkelt linje ved hjælp af den `azure` metakoden-pakke. Siden ikke alle pakker i denne metakoden-pakke er udgivet som stabil endnu, den `azure` metakoden-pakke er stadig i Vis udskrift. Dog kan core-pakker fra kode kvalitet/fuldstændighed perspektiver betragtes som "stabil" på nuværende tidspunkt
- Det vil være officielt mærket som sådan synkroniseret med andre sprog så tidligt som muligt. Vi har ikke planer om yderligere større ændringer indtil da.

Eftersom den er en preview-versionen, skal du bruge den `--pre` flag:

```console
   $ pip install --pre azure
```
   
eller direkte

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Få flere pakker

[Python pakke indeks][] (PyPI) har et omfattende udvalg af Python biblioteker.  Hvis du vælger at installere en Distro, får du allerede har de fleste af de interessante bit for forskellige scenarier fra webudvikling til tekniske beregninger.


## <a name="python-tools-for-visual-studio"></a>Python Tools til Visual Studio

[Python Tools til Visual Studio][] (PTVS) er et gratis/OSS plug-in fra Microsoft, som omdannes VS til et færdiglavet Python IDE:

![How-til-Installer-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Brug af PTVS er valgfrit, men det anbefales, når du får support Python og Web Project/løsning, fejlfinding, profiler, interaktive vindue, skabelonredigering og Intellisense.

PTVS gør det også nemt skal installeres til Microsoft Azure med support til installation [Cloud Services][] og [websteder][].

PTVS fungerer med din eksisterende Visual Studio 2013 eller 2015-installation.  Se [Python Tools til Visual Studio]til dokumentation, overførsler og diskussioner.  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Azure scenarier, hvor Linux og MacOS

For Linux eller MacOS, primære Azure scenarier, der understøttes:

1. Forbrug Azure Services ved hjælp af klientbiblioteker til Python

2. Køre din app i en Linux VM

3. Udvikle og publicere til Azure websteder ved hjælp af ciffer

Det første scenario gør det muligt at redigere omfattende webapps, der udnytte Azure PaaS funktioner såsom [blob-lager][], [kø lagerplads][], [tabellagring][] osv via Pythonic indpakningsmaterialet til Azure REST API'er. Disse fungerer identisk på Windows, Mac og Linux.  Du kan også bruge disse klientbiblioteker fra din lokale computer eller en Linux VM kører på Azure.

VM scenarie skal du blot starte en Linux VM efter eget valg (Ubuntu, CentOS, Suse) og kør/administrere hvordan ud.  Du kan køre [IPython][] Erstat/notesbog på din computer og Mac-Windows/Linux og browseren pege på en Linux eller Windows flere processorer VM kører IPython Engine på Azure som et eksempel. Se [IPython notesbog på Azure][] selvstudiet kan finde flere oplysninger.

Du kan finde oplysninger om konfiguration af en Linux VM, [Opret et virtuelt kører Linux][] -selvstudium.

Bruger ciffer installation, kan du udvikle et Python webprogram og publicere den til et websted, Azure fra operativsystemer.  Når du klikker din lager til Azure, oprettes der automatisk et virtuelt miljø og pip installere din nødvendige pakker.

Se selvstudierne til [Oprettelse af websteder med Django][], [Oprettelse af websteder med flasker][]og [Oprettelse af websteder med anbringes][]kan finde flere oplysninger om udvikle og publicere Azure websteder. Du kan finde mere generelle oplysninger om brug af en hvilken som helst WSGI-kompatibel framework [Konfigurere Python med Azure websteder][].


## <a name="additional-software-and-resources"></a>Yderligere Software og ressourcer:

* [Azure SDK for Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK for Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Officielle Azure prøver for Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Konsistente Analytics Python fordeling][]
* [Enthought Python fordeling][]
* [ActiveState Python fordeling][]
* [SciPy - en pakke med videnskabelig Python biblioteker][]
* [NumPy - et tal bibliotek til Python][]
* [Django Project – en fuldt udviklet web framework/CMS][]
* [IPython - en avanceret Erstat/notesbog til Python][]
* [IPython notesbog på Azure][]
* [Python Tools til Visual Studio på GitHub][]
* [Python Developer Center](/develop/python/)

[Konsistente Analytics Python fordeling]: http://continuum.io
[Enthought Python fordeling]: http://www.enthought.com
[ActiveState Python fordeling]: http://www.activestate.com
[www.Python.org]: http://www.python.org
[www.continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - en pakke med videnskabelig Python biblioteker]: http://www.scipy.org
[NumPy - et tal bibliotek til Python]: http://www.numpy.org
[Django Project – en fuldt udviklet web framework/CMS]: http://www.djangoproject.com
[IPython - en avanceret Erstat/notesbog til Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython notesbog på Azure]: virtual-machines-linux-jupyter-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Websteder]: web-sites-python-ptvs-django-mysql.md
[Python Tools til Visual Studio]: http://aka.ms/ptvs
[Python Tools til Visual Studio på GitHub]: https://github.com/microsoft/ptvs
[Python pakke indeks]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Oprette en virtuel maskine kører Linux]: virtual-machines-linux-quick-create-cli.md
[Oprette websteder med Django]: web-sites-python-create-deploy-django-app.md
[Oprette websteder med flasker]: web-sites-python-create-deploy-bottle-app.md
[Oprette websteder med anbringes]: web-sites-python-create-deploy-flask-app.md
[Konfigurere Python med Azure websteder]: web-sites-python-configure.md
[tabellagerplads]: storage-python-how-to-use-table-storage.md
[kø lagerplads]: storage-python-how-to-use-queue-storage.md
[BLOB-lager]: storage-python-how-to-use-blob-storage.md
