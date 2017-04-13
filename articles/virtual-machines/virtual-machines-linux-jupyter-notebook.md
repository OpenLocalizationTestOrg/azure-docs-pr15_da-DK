<properties
    pageTitle="Oprette en Jupyter/IPython notesbog | Microsoft Azure"
    description="Få mere at vide, hvordan du kan installere Jupyter/IPython notesbogen på en Linux virtuel maskine, der er oprettet med ressource manager implementeringsmodel i Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Jupyter notesbog på Azure

[Jupyter project](http://jupyter.org), tidligere [IPython projekt](http://ipython.org), indeholder en samling af værktøjer til videnskabelig computing ved hjælp af effektive interaktive brugergrænseflader, der kombinerer programkode med oprettelse af et live computational dokument. Filerne notesbog kan indeholde vilkårlig tekst, matematiske formler, Indtast kode, resultater, grafik, videoer og enhver anden type medier, der kan vise en moderne webbrowser. Om du er absolut ny bruger af Python og vil se den i en sjov og interaktive miljø eller gøre nogle alvorlige parallelt/tekniske computing, er Jupyter notesbogen et godt valg.

![Skærmbillede](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) ved hjælp af SciPy og Matplotlib-pakker til at analysere strukturen i en lyd optagelse.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter to måder: Azure notesbøger eller brugerdefineret installation
Azure leverer en tjeneste, som du kan bruge til [Kom hurtigt i gang ved hjælp af Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Ved hjælp af tjenesten Azure notesbog, kan du nemt få adgang til Jupyter's web-tilgængelige grænsefladen til SVG databehandlingsressourcer med alle funktionerne fra Python og dens mange biblioteker.  Da installationen håndteres af tjenesten, kan brugere kan få adgang til disse ressourcer uden brug af administration og konfiguration af brugeren.

Hvis tjenesten notesbog ikke fungerer for det pågældende scenarie skal du fortsætte til at læse denne artikel, der skal vises, hvordan du installere Jupyter notesbogen på Microsoft Azure, ved hjælp af Linux virtuelle maskiner (VM'er).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Oprette og konfigurere en VM i Azure

Det første trin er at oprette en virtuel maskine (VM), der kører på Azure.
Denne VM er et komplet operativsystem i skyen og bruges til at køre Jupyter notesbogen. Azure er stand til at køre både Linux og Windows virtuelle maskiner, og vi kommer ind på opsætningen af Jupyter på begge typer virtuelle computere.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Oprette en Linux VM og åbne en port til Jupyter

Følg vejledningen angivne [her] [ portal-vm-linux] til at oprette en virtuel maskine for den stokastiske variabel *Ubuntu* . Dette selvstudium bruger Ubuntu Server 14.04 LTS. Vi vil antager, at brugeren navn *azureuser*.

Når den virtuelle maskine installerer har vi brug at åbne en sikkerhedsregel på sikkerhedsgruppen netværk.  Gå til **Netværk sikkerhedsgrupper** fra Azure-portalen, og Åbn fanen for den svarer til din VM sikkerhedsgruppe. Skal du tilføje en regel for indgående sikkerhed med følgende indstillinger: **TCP** for protokollen, **\*** for kildeport (offentlig) og **9999** for destinationsporten (privat).

![Skærmbillede](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Mens du er i dit netværk sikkerhedsgruppe, klikke på **Netværksgrænseflader** og Bemærk den **Offentlige IP-adresse** , som det skal bruges til at oprette forbindelse til din VM i næste trin.

## <a name="install-required-software-on-the-vm"></a>Installere software, der kræves på VM

Hvis du vil køre Jupyter notesbogen på vores VM, skal vi først installere Jupyter og dets afhængigheder. Oprette forbindelse til din linux vm ved hjælp af ssh og brugernavn og adgangskode par du valgte, da du oprettede vm. I dette selvstudium skal vi bruge trykfarver og oprette forbindelse fra Windows.

### <a name="installing-jupyter-on-ubuntu"></a>Installerer Jupyter på Ubuntu
Installere Anaconda, populære data videnskabelige python fordel ved hjælp af en af de links, der er angivet fra [Konsistente Analytics](https://www.continuum.io/downloads).  Fra og til at skrive dette dokument, den under links, der er mest op til dato versioner.

#### <a name="anaconda-installs-for-linux"></a>Anaconda installationer for Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64-bit</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64-bit</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32-bit</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32-bit</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Installere Anaconda3 2.3.0 64-bit på Ubuntu
Som eksempel er dette, hvordan du kan installere Anaconda på Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Skærmbillede](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Konfiguration af Jupyter og bruge SSL
Når du har installeret har vi brug at tage et øjeblik på at konfigurere konfigurationsfilerne til Jupyter. Hvis du oplever troubles med konfiguration af Jupyter kan det være praktisk at se på [Jupyter dokumentation til kørsel af en notesbog Server](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Næste vi `cd` til mappen profil for at oprette vores SSL-certifikat og redigere konfigurationsfil profiler.

Brug følgende kommando på Linux.

    cd ~/.jupyter

Brug følgende kommando til at oprette SSL-certifikat (Linux og Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Bemærk, at da vi opretter et selvsigneret SSL-certifikat, når du opretter forbindelse til notesbogen browseren kan give dig en sikkerhedsadvarsel.  Du vil bruge et korrekt signeret certifikat, der er knyttet til din organisation til langsigtede daglig drift.  Da Certifikatstyring er ikke medtaget i denne demo, vil vi overholder du et selvsigneret certifikat til nu.

Ud over at bruge et certifikat, skal du også angive en adgangskode for at beskytte din notesbog mod uautoriseret brug.  Af sikkerhedsmæssige årsager bruges Jupyter krypterede adgangskoder i dets konfigurationsfil, så skal du kryptere adgangskoden først.  IPython indeholder en funktion for at gøre dette, og Kør følgende kommando ved en kommandoprompt.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Dette bliver du bedt om en adgangskode og bekræftelse, og derefter skriver adgangskoden. Bemærk dette til det følgende trin.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Dernæst skal vi vil redigere profilens konfigurationsfil, som er den `jupyter_notebook_config.py` fil i den mappe, du befinder dig i.  Bemærk, at denne fil ikke kan findes – kun oprette den, hvis det er tilfældet.  Denne fil har en lang række felter, og som standard er alle kommenterede ud.  Du kan åbne denne fil med en hvilken som helst tekstredigeringsprogram af dine synes godt om, og du skal sikre, at den mindst har følgende indhold. **Sørg for at erstatte c.NotebookApp.password i config med sha1 fra det forrige trin**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Køre Jupyter notesbog

På dette tidspunkt er vi klar til at starte Jupyter notesbogen. Gå til den mappe, du vil gemme notesbøger i og starte Jupyter notesbog server med følgende kommando for at gøre dette.

    /anaconda3/bin/jupyter-notebook

Du bør nu kunne få adgang til notesbogen Jupyter på adressen, `https://[PUBLIC-IP-ADDRESS]:9999`.

Når du først få adgang til din notesbog, logonsiden beder dig om din adgangskode. Og når du logger på, du vil se "Jupyter notesbog dashboardet for", som er ontrol center til alle notesbog-handlinger.  Du kan oprette nye notesbøger og åbne eksisterende fra denne side.

![Skærmbillede](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Bruge Jupyter notesbog

Hvis du klikker på knappen **Ny** , vises følgende startsiden.

![Skærmbillede](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

Området, der er markeret med en `In []:` meddelelse er i chatten, og her kan du skrive en gyldig Python kode og den udføres, når du klikker på `Shift-Enter` eller klik på ikonet "Afspil" (den højre trekant, der peger på værktøjslinjen).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>En effektiv paradigme: live computational dokumenter med rich media

Notesbogen selve skal føler meget neutralt for alle, der er brugt Python og en word-processor, fordi det er nogle måder en blanding af begge: Du kan udføre blokke med Python kode, men du kan også holde noter og anden tekst ved at ændre typografien for en celle fra "-kode" til "Tabsbeløb" ved hjælp af menuen ned på værktøjslinjen.

Jupyter er meget mere end en word-processor, som det giver mulighed for at blande beregning og rich media (tekst, grafik, video og stort set alt, der kan få vist en moderne webbrowser). Du kan blande, tekst, kode, videoer og meget mere!

![Skærmbillede](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

Og med en potens af Pythons mange fremragende billedbiblioteker til videnskabelige og tekniske computing, på følgende skærmbillede, en simpel beregning kan udføres med det samme øget som en kompleks netværk analyse, alle i et miljø.

Denne paradigme af blanding af n'te potens, hvor webdelen moderne med live beregning indeholder mange muligheder, og er velegnet til skyen; Notesbogen kan bruges:

* Arbejde på et problem, som en beregningsressource notesblokken til at registrere forsøgsfiskeri.

* Dele resultater med kolleger, enten 'live' computational formular eller i papirformat formater (HTML, PDF-fil).

* Ændre den for at distribuere og præsentere direkte undervisning materialer, der involverer beregning, så elever kan med det samme eksperimentere med den reelle kode, og Udfør det igen interaktivt.

* Angive "eksekverbare papir", der præsenterer resultaterne af opslag på en måde, som kan være med det samme gengives, valideret og forlænget med andre.

* Som en platform for samarbejde computing: flere brugere kan logge på den samme notesbog server til at dele en direkte computational session.


Hvis du går til IPython kilde kode [lager][], kan du finde en hel mappe med eksempler på notesbog, du kan hente og derefter eksperimentere med på din egen Azure Jupyter VM.  Du skal blot hente den `.ipynb` filer fra webstedet og overføre dem til dashboardet i notesbogen Azure VM (eller hente dem direkte i VM).

## <a name="conclusion"></a>Konklusion

Jupyter notesbogen indeholder en effektiv grænseflade til adgang til en potens af Python økosystemet på Azure interaktivt.  Det dækker et bredt udvalg af brugen tilfælde, herunder enkel udforske og læ Python, dataanalyse og visualisering, simulering og parallel databehandling. De resulterende notesbogdokumenter indeholder en komplet oversigt over de beregninger, der udføres og kan deles med andre Jupyter brugere.  Jupyter notesbogen kan bruges som et lokalt program, men det er velegnet til skyen installationer på Azure

De grundlæggende funktioner i Jupyter findes også i Visual Studio via [Python Tools til Visual Studio][] (PTVS). PTVS er en gratis og open source-plug-in'en fra Microsoft, der omdannes Visual Studio til et avanceret Python udviklingsmiljø, der indeholder en Avanceret editor med IntelliSense, fejlfinding, status og parallel databehandling med integration.

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Python Developer Center](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[lager]: https://github.com/ipython/ipython
[Python Tools til Visual Studio]: http://aka.ms/ptvs
