<properties
    pageTitle="Klargøre Linux Data videnskabelige Virtual Machine | Microsoft Azure"
    description="Konfigurere og oprette en Linux Data videnskabelige virtuel maskine på Azure kan du gøre analyser og machine learning."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />

# <a name="provision-the-linux-data-science-virtual-machine"></a>Klargøre Linux Data videnskabelige Virtual Machine

Linux Data videnskabelige Virtual Machine er en Azure virtuel maskine, som følger med en samling af forudinstalleret værktøjer. Disse funktioner er almindeligt brugte for at gøre data analyser og machine learning. Vigtige softwarekomponenterne er:

- Microsoft R Server Developer Edition
- Anaconda Python fordeling (versioner 2.7 og 3.5), herunder populære data analysis biblioteker
- JupyterHub - en flerbruger Jupyter notesbog server, der understøtter R, Python, Julia kerner
- Azure-lager Explorer
- Azure kommandolinjen (CLI) for at administrere Azure ressourcer
- PostgresSQL Database
- Learning værktøjsmaskiner
    - [Computational netværk værktøjskassen (CNTK)](https://github.com/Microsoft/CNTK): en deep læ software værktøjskassen fra Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): en hurtig maskine læ filsystem, der understøtter teknikker såsom online, hashing-, allreduce, reduktion, learning2search, aktiv, og interaktive læring.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): et værktøj, give hurtige og præcise øget træet implementering.
    - [Rattle](http://rattle.togaware.com/) (den R Analytical værktøj til at få mere at vide nemt): et værktøj, der gør komme i gang med data analyser og machine learning i R nemt med undersøgelse af grafiske brugergrænseflade-baserede data, og -modellering med automatisk oprettelse af R kode.
- Azure SDK i Java, Python, node.js, fonetisk, PHP
- Biblioteker i R og Python til brug i Azure maskine læring og andre Azure tjenester
- Værktøjer til udvikling af og redaktører (Eklipse, Emacs, gedit, vi)

At gøre data videnskabelige omfatter gentagelse på en række opgaver:

1. Søge efter ved indlæsning og forbehandling af data
2. Opbygge og teste modeller
3. Implementering af modeller til forbrug i intelligente programmer

Data forskere bruge forskellige værktøjer til at fuldføre disse opgaver. Det kan være meget tidskrævende at finde de relevante versioner af softwaren, og derefter for at hente, samle og installere disse versioner.

Linux Data videnskabelige Virtual Machine kan lette denne arbejdsbyrde væsentligt. Bruge det til at Kom hurtigt i gang projektet analyser. Det gør det muligt at arbejde på opgaver på forskellige sprog, herunder R, Python, SQL, Java og C++. Eklipse indeholder en IDE at udvikle og teste din kode, der er nem at bruge. Azure SDK inkluderet i VM gør det muligt at oprette dine programmer med forskellige tjenester på Linux for Microsoft cloud-platformen. Desuden har du adgang til andre sprog som fonetisk, Perl, PHP og node.js, der også er forudinstalleret.

Der er ingen software gebyrer for dette data videnskabelige VM billede. Du betaler kun de Azure hardware-Brugsgebyrer, vurderes baseret på størrelsen af den virtuelle maskine, som du klargør med VM billede. Flere oplysninger om Beregn gebyrerne kan findes på [listen over siden på Azure Marketplace VM ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).


## <a name="prerequisites"></a>Forudsætninger

Før du kan oprette en Linux Data videnskabelige virtuel maskine, skal du have følgende:

- **En Azure-abonnement**: for at få et, skal du se [få Azure gratis prøveversion](https://azure.microsoft.com/free/).
- **En Azure-lager konto**: for at oprette en, under [oprette en Azure-lager-konto](storage-create-storage-account.md#create-a-storage-account). Du kan også kontoen lagerplads, kan oprettes som en del af processen med at oprette VM, hvis du ikke vil bruge en eksisterende konto.


## <a name="create-your-linux-data-science-virtual-machine"></a>Oprette din Linux Data videnskabelige virtuelle maskine

Her er trinnene for at oprette en forekomst af Linux Data videnskabelige Virtual Machine:

1.  Gå til den virtuelle maskine listen over på [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.   Klik på **Opret** (nederst) for at få vist guiden. ![konfigurere-data-videnskabelige-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.   De følgende afsnit indeholder input for hvert trin i guiden (optælles til højre i den foregående figur) bruges til at oprette Microsoft Data videnskabelige Virtual Machine. Her er de materialer, der er behov for at konfigurere hvert af disse trin:

    en. **Grundlæggende om**:

  - **Navn**: navnet på din data videnskabelige server, du opretter.
  - **Brugernavn**: første konto logon ID.
  - **Adgangskode**: første kontoadgangskode (du kan bruge SSH offentlig nøgle i stedet for adgangskode).
  - **Abonnement**: Hvis du har mere end ét abonnement, kan du vælge den, som computeren er oprettet og faktureret. Du skal have ressource oprettelse af rettigheder til dette abonnement.
  - **Ressourcegruppe**: Du kan oprette en ny eller bruge en eksisterende gruppe.
  - **Placering**: Vælg datacenteret, der er mest relevante. Det er normalt datacenteret, der har mest muligt ud af dine data, eller er tættest på din fysiske placering til hurtigste netværksadgang.

    b. **Størrelse**:

  - Vælg en af de server-datatyper, der opfylder dine funktionelle krav og omkostningsbegrænsninger. Vælg **Vis alle** at få vist flere valg af VM størrelser.

    c. **Indstillinger**:

  - **Disken Type**: Vælg **Premium** , hvis du foretrækker en dækkende state-drev (SSD). Ellers skal du vælge **Standard**.
  - **Lagerplads konto**: Du kan oprette en ny firmapost Azure-lager i dit abonnement, eller brug et eksisterende dokument på samme placering, som blev valgt på det **grundlæggende** trin i guiden.
  - **Andre parametre**: I de fleste tilfælde kan du vælge at bruge standardværdierne. Hold markøren over linket informativ for hjælp til de specifikke felter for at overveje ikke-standardværdier.

    d. **Oversigt**:

  - Bekræft, at alle oplysninger, du har angivet er korrekte.

    e. **Købe**:

  - Klik på **Køb**for at starte klargøringen skal. Et link, der overføres til vilkårene for posteringen. VM har ikke eventuelle yderligere gebyrer ud over Beregn til den server størrelse, du vælger i trinnet **størrelse** .

Klargøringen fører omkring 10-20 minutter. Status for klargøringen vises på Azure-portalen.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Sådan får du adgang Linux Data videnskabelige Virtual Machine

Når VM er oprettet, kan du logge på til den ved hjælp af SSH. Brug de legitimationsoplysninger, du oprettede i afsnittet **grundlæggende funktioner** i trin 3 for grænsefladen tekst shell. I Windows, kan du hente en SSH client værktøj som [trykfarver](http://www.putty.org). Hvis du foretrækker grafiske skrivebordet (X Windows System), kan du bruge X11 videresendelse på trykfarver eller installere X2Go klienten.

>[AZURE.NOTE] X2Go klienten udført betydeligt bedre end X11 videresendelse i test. Vi anbefaler, at du bruger X2Go-klienten til en grafisk computeren grænseflade.


## <a name="installing-and-configuring-x2go-client"></a>Installation og konfiguration af X2Go klient

Linux VM er allerede klargjort med X2Go server og klar til at acceptere klientforbindelser. For at oprette forbindelse til Linux VM grafiske skrivebordet, skal du gøre følgende på din klient:

1. Hent og Installer X2Go klienten for din klient platform fra [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Kør X2Go klienten, og vælg **Ny Session**. Der åbnes et vindue med konfiguration med flere faner. Angiv følgende konfigurationsparametre:
    * **Under fanen session**:
        - **Host**: værtsnavn eller IP-adressen på din Linux Data videnskabelige VM.
        - **Login**: brugernavn på Linux VM.
        - **SSH Port**: Lad feltet stå på 22, standardværdien.
        - **Session Type**: ændre værdien til XFCE. Linux VM understøtter i øjeblikket kun XFCE skrivebord.
    * **Medier under fanen**: Du kan deaktivere understøttelse af lyd og udskrivning, hvis du ikke behøver at bruge dem-klienten.
    * **Delte mapper**: Hvis du vil mapper fra din klientcomputere, der er tilsluttet på Linux VM, tilføje klient computer-mapper, du vil dele med VM under denne fane.

Når du logger på VM ved hjælp af SSH klient eller XFCE grafiske skrivebord via klienten X2Go, er du klar til at begynde at bruge de værktøjer, der er installeret og konfigureret på VM. Du kan se genveje i menuen programmer og ikoner på skrivebordet for mange af værktøjerne på XFCE.


## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Funktioner, der er installeret på den Linux Data videnskabelige virtuelle maskine

### <a name="microsoft-r-open"></a>Åbn Microsoft R
R er et af de mest populære sprog til dataanalyse og maskine læring. Hvis du vil bruge R for dine analyser, har VM Microsoft R Åbn (MRO) med matematisk kernen bibliotek (MKL). MKL optimerer matematiske handlinger, der er fælles for analytical algoritmer. MRO er 100 procent kompatibel med CRAN-R, og en af de R-biblioteker, der er udgivet i CRAN kan være installeret på MRO. Du kan redigere dine R-programmer på en af standard-redaktører, som vi, Emacs eller gedit. Du kan også hente og bruge andre IDEs, som [RStudio](http://www.rstudio.com). Gøre arbejdet lettere findes et simpelt script (installRStudio.sh) i mappen **/dsvm/tools** , der installeres RStudio. Hvis du bruger redigeringsprogrammet Emacs, er arbejde med R-filer i editoren Emacs Bemærk, at Emacs pakke ESS (Emacs Speaks statistik), hvilket forenkler forudinstalleret.

For at starte R, skal skrive du bare **R** i shell. Du føres til et interaktivt miljø. Hvis du vil udvikle din R-program, du typisk bruger redaktør som Emacs eller vi eller gedit, og derefter køre scripts i R. Hvis du har installeret RStudio, har du en fuldstændig grafiske ide-miljø at udvikle dit R-program.

Der er også en R script for dig at installere de [øverste 20 R-pakker](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) , hvis du vil have. Dette script kan kun køres, når du er i den interaktive R-brugergrænseflade, som kan angives (som nævnt) ved at skrive **R** i shell.  

### <a name="python"></a>Python
Til udvikling ved hjælp af Python, er Anaconda Python fordeling 2.7 og 3.5 installeret. Denne fordeling indeholder grundlæggende Python sammen med om 300 af de mest populære matematisk, teknisk og data analytics pakker. Du kan bruge standard tekst redaktører. Desuden kan du bruge Spyder, en Python-IDE, der følger med Anaconda Python salgsdistributioner. Spyder kræver en grafisk skrivebord eller X11 videresendelse. En genvej til Spyder er angivet i den grafiske på computeren.

Da vi har både Python 2.7 og 3.5, skal du aktivere specifikt den ønskede Python version, du vil arbejde på i den aktuelle session. Aktiveringsprocessen indstiller variablen STIEN til den ønskede version af Python.

For at aktivere Python 2.7 skal du køre følgende fra shell:

    source /anaconda/bin/activate root

Python 2.7 er installeret på */anaconda/bin*.

For at aktivere Python 3.5 skal du køre følgende fra shell:

    source /anaconda/bin/activate py35


Python 3.5 er installeret på */anaconda/envs/py35/bin*.

Hvis du vil anvende en Python interaktive session, skal du blot skrive **python** i shell. Hvis du er på en grafisk grænseflade eller har X11 viderestilling af sæt op, kan du skrive **spyder** for at starte Python IDE.

### <a name="jupyter-notebook"></a>Jupyter notesbog

Anaconda fordeling leveres også med en Jupyter notesbog, et miljø til at dele kode og analyse. Jupyter notesbogen åbnes via JupyterHub. Du logge på med dit lokale Linux-brugernavn og din adgangskode.

Jupyter notesbog serveren er konfigureret udfyldt med Python 2, 3 Python og R kerner. Der findes et ikon på skrivebordet med navnet "Jupyter notesbog" for at starte browseren for at få adgang til notesbog-serveren. Hvis du er på VM via SSH eller X2Go-klient, du kan også besøge [https://localhost:8000 /](https://localhost:8000/) til at få adgang til Jupyter notesbog-serveren.

>[AZURE.NOTE] Fortsæt, hvis du bliver advaret et certifikat.

Du kan få adgang til Jupyter notesbog serveren fra en hvilken som helst vært. Du skal blot skrive *https://\<VM DNS navn eller IP-adresse\>: 8000 /*

>[AZURE.NOTE] Port 8000 er som standard åbnes i firewallen, når VM er klargjort.

Vi har pakket eksempel notesbøger – én i Python og én i R. Du kan se linket til eksemplerne på hjemmesiden notesbog når du godkende til Jupyter notesbog ved hjælp af dit lokale Linux-brugernavn og din adgangskode. Du kan oprette en ny notesbog ved at vælge **Ny**og derefter kernen relevante sprog. Hvis du ikke kan se knappen **Ny** , skal du klikke på ikonet **Jupyter** øverst til venstre for at gå til startsiden for notesbog serveren.


### <a name="ides-and-editors"></a>IDEs og redaktører

Du har et valg af flere kode redaktører. Dette omfatter vi/VIM, Emacs, gEdit og Eklipse. gEdit og Eklipse er grafiske redaktører, og du skal være logget på et grafisk skrivebord bruges. Disse editors har skrivebord og programmer i menugenveje til start dem.

**VIM** og **Emacs** er tekstbaserede redaktører. Vi har installeret en tilføjelsesprogram pakke kaldet Emacs Speaks statistik (ESS), der gør det nemmere i redigeringsprogrammet Emacs arbejde med R Emacs. Yderligere oplysninger finder du på [ESS](http://ess.r-project.org/).

**Eklipse** er åben kildekode, extensible IDE, der understøtter flere sprog. Java udviklere edition er den forekomst, der er installeret på VM. Plug-ins er tilgængelige for flere populære sprog, der kan være installeret for at udvide Eklipse-miljø. Vi har også et plug-in installeret i Eklipse kaldet **Azure-værktøjskassen til Eklipse**. Det gør det muligt at oprette, udvikle, teste og installere Azure-programmer ved hjælp af den Eklipse udviklingsmiljø, der understøtter sprog som Java. Der er også en **Azure SDK til Java** , der giver adgang til forskellige Azure-tjenester fra en Java-miljø. Flere oplysninger om Azure-værktøjskassen til Eklipse kan findes på [Azure-værktøjskassen til Eklipse](../azure-toolkit-for-eclipse.md).

**LaTex** installeres via pakken texlive sammen med en Emacs tilføjelsesprogrammet [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) -pakke, hvilket forenkler redigering af din LaTex dokumenter i Emacs.  

### <a name="databases"></a>Databaser

#### <a name="postgres"></a>Postgres
Åbn kildedatabasen **Postgres** er tilgængelig på VM, med de tjenester, der kører og initdb allerede fuldført. Du skal stadig oprette databaser og brugere. Yderligere oplysninger finder du i [Postgres dokumentation](https://www.postgresql.org/docs/).  

####  <a name="graphical-sql-client"></a>Grafiske SQL client
**SQuirrel SQL**, en grafisk SQL-klient har fået tildelt, til at oprette forbindelse til forskellige databaser (som Microsoft SQL Server, Postgres og MySQL) og køre SQL-forespørgsler. Du kan køre ud fra en grafisk Fjernskrivebord-session (med X2Go klienten, for eksempel). Hvis du vil anvende SQuirrel SQL, kan du starte det fra ikonet på skrivebordet eller køre følgende kommando på shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Konfigurere din drivere og database aliasser før den første anvendelse. JDBC driverne er placeret på:

*/usr/share/Java/jdbcdrivers*

Du kan finde flere oplysninger [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Kommandolinjen værktøjer til at få adgang til Microsoft SQL Server

Pakken ODBC-driver til SQL Server leveres også med to kommandolinjen værktøjer:

**BCP**: bcp utility flere kopierer data mellem en forekomst af Microsoft SQL Server og en datafil i et brugerdefineret format. Værktøjet bcp kan bruges til at importere stort antal nye rækker til SQL Server-tabeller eller for at eksportere data af tabeller i datafiler. Hvis du vil importere data til en tabel, skal du enten bruge en format-fil, der er oprettet for den pågældende tabel eller forstå strukturen i tabellen og typerne data, der gælder for tabellens kolonner.

Yderligere oplysninger finder du i [tilslutning med bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Du kan angive Transact-SQL-sætninger med sqlcmd utility samt system procedurer og scripts filer ved kommandoprompten. Denne funktion bruges ODBC til at udføre Transact-SQL batches.

Yderligere oplysninger finder du i [tilslutning med sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Der er nogle forskelle i dette værktøj mellem Linux og Windows-platforme. Se i dokumentationen til detaljer.


#### <a name="database-access-libraries"></a>Database access biblioteker

Der findes biblioteker i R og Python til access-databaser.

- I R kan den **RODBC** pakke eller **dplyr** pakke du forespørge eller udføre SQL-sætninger på databaseserveren.
- I Python indeholder biblioteket **pyodbc** databaseadgang med ODBC som det underliggende lag.  

For at åbne **Postgres**:

- Brug funktionen pakke **RPostgreSQL**fra hoved:.
- Brug biblioteket **psycopg2** fra Python:.


### <a name="azure-tools"></a>Azure værktøjer
Følgende Azure værktøjer er installeret på VM:

- **Azure kommandolinjen**: feltet Azure CLI gør det muligt at oprette og administrere Azure ressourcer via shell-kommandoer. Hvis du vil anvende værktøjerne Azure, skal du bare skrive **azure Hjælp**. Se [Azure CLI dokumentation siden](../virtual-machines-command-line-tools.md)kan finde flere oplysninger.
- **Microsoft Azure lagerplads Explorer**: Microsoft Azure-lager Explorer er et grafisk værktøj, der bruges til at gennemse de objekter, du har gemt i kontoen Azure-lager og til at uploade og downloade data til og fra Azure BLOB. Du kan få adgang til Storage Explorer fra ikonet skrivebordsgenvej. Du kan aktivere det fra en meddelelse om shell ved at skrive **StorageExplorer**. Du skal være logget på fra en X2Go-klient eller har X11 viderestilling af sæt op.
- **Azure biblioteker**: følgende er nogle af de forudinstallerede biblioteker.

 - **Python**: feltet Azure-relaterede biblioteker i Python, der er installeret er **azure**, **azureml**, **pydocumentdb**og **pyodbc**. Med de første tre biblioteker, du kan få adgang til Azure lagerplads services, Azure maskine læring og Azure DocumentDB (en NoSQL database på Azure). Biblioteket fjerde pyodbc (sammen med Microsoft ODBC-driveren til SQL Server), giver adgang til SQL Server Azure SQL-Database og Azure SQL Data Warehouse fra Python ved hjælp af en ODBC-grænseflade. Angiv **pip liste** for at få vist alle de viste biblioteker. Sørg for at køre denne kommando i både Python 2.7 og 3,5 miljøer.
 - **R**: feltet Azure-relaterede biblioteker på R, der er installeret er **AzureML** og **RODBC**.
 - **Java**: listen over Azure Java biblioteker kan findes i mappen **/dsvm/sdk/AzureSDKJava** på VM. De vigtigste biblioteker er Azure-lager og styring API'er, DocumentDB og JDBC drivere til SQL Server.  

Du kan få adgang til [Azure portal](https://portal.azure.com) fra forudinstalleret Firefox-browseren. På Azure-portalen, kan du oprette, administrere og overvåge Azure ressourcer.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning er en fuldt administreret skybaseret tjeneste, der gør det muligt at opbygge, installere og dele skønnet analytics løsninger. Du opretter dit forsøg og datamodeller fra Azure Machine Learning Studio. Det kan åbnes fra en webbrowser på data videnskabelige virtuel maskine ved at besøge [Microsoft Azure Machine Learning](https://studio.azureml.net).

Når du logger på Azure maskine læ Studio, har du adgang til en forsøg lærred hvor du kan bygge et logisk forløb for den computer, læ algoritmer. Du kan også har adgang til en Jupyter notesbog hostes på Azure Machine Learning og kan arbejde problemfrit med forsøg i Machine Learning Studio. Giver muligheder machine learning-modeller, du har oprettet ved at sætte dem i en grænseflade til webtjenester. Dette gør det muligt for klienter, der er skrevet på en hvilken som helst sprog for at kalde prognoser fra machine learning-modeller. Du kan finde yderligere oplysninger finder [Machine Learning dokumentation](https://azure.microsoft.com/documentation/services/machine-learning/).

Du kan også oprette din modeller på R eller Python på VM og derefter installere det i fremstilling på Azure Machine Learning. Vi har installeret biblioteker på R (**AzureML**) og Python (**azureml**) for at aktivere denne funktion.

Oplysninger om, hvordan du installerer modeller i R og Python til Azure Machine Learning finder du [Ti ting, du kan udføre på Data videnskabelige virtuelt](machine-learning-data-science-vm-do-ten-things.md) (især afsnittet "oprette modeller ved hjælp af R eller Python og giver muligheder for dem ved hjælp af Azure Machine Learning").

>[AZURE.NOTE] Disse instruktioner blev skrevet til Windows-versionen af Data videnskabelige VM. Men oplysningerne der implementerer modeller til Azure maskine undervisning er gældende for Linux VM.

### <a name="machine-learning-tools"></a>Learning værktøjsmaskiner

VM følger med nogle få machine learning-værktøjer og algoritmer, der er udfyldt kompileret og forudinstalleret lokalt. Dette omfatter:

* **CNTK** (Computational netværk værktøjskassen fra Microsoft Research): en deep læ værktøjskassen.
* **Vowpal Wabbit**: en hurtig online learning algoritme.
* **xgboost**: et værktøj, der indeholder algoritmer, der er optimeret, øget trævisningen.
* **Python**: Anaconda Python leveres med machine learning algoritmer med biblioteker som Scikit-få mere at vide. Du kan installere andre biblioteker ved hjælp af den `pip install` kommandoen.
* **R**: et omfattende bibliotek med computeren learning funktioner er tilgængelige for R. Nogle af de biblioteker, der er forudinstalleret er lm, glm, randomForest, rpart. Andre biblioteker kan installeres ved at køre:

        install.packages(<lib name>)

Her er nogle flere oplysninger om de første tre machine learning værktøjer på listen.

#### <a name="cntk"></a>CNTK
Dette er åben kildekode, deep læ værktøjskassen. Det er et værktøj på kommandolinjen (cntk), og der allerede findes i STIEN.

For at køre en grundlæggende eksempel skal du udføre følgende kommandoer i shell:

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

Model output er i *~/cntkdemo/Output/Models*.

Du kan finde flere oplysninger i afsnittet CNTK [GitHub](https://github.com/Microsoft/CNTK)og [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).


#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit er en maskine læ system, som bruger teknikker såsom online, hashing-, allreduce, reduktion, learning2search, aktiv, og interaktive læring.

For at køre værktøjet på et eksempel på meget grundlæggende skal du gøre følgende:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Der er andre, større demoer i mappen. Se [dette afsnit af GitHub](https://github.com/JohnLangford/vowpal_wabbit)og [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)kan finde flere oplysninger om VW.

#### <a name="xgboost"></a>xgboost
Dette er et bibliotek, der er designet og optimeret til øget (træ) algoritmer. Formålet med dette bibliotek er til at overføre begrænsningerne beregning af maskiner til ekstreme behov for at give store træet øger, der er SVG, bærbare og præcise.

Det er angivet en kommandolinje samt en R-bibliotek.

Hvis du vil bruge dette bibliotek i R, kan du starte en interaktiv R-session (lige ved at skrive **R** i shell), og Indlæs biblioteket.

Her er et simpelt eksempel, du kan køre i R prompt:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Hvis du vil køre kommandolinjen xgboost, er her kommandoer til at udføre i shell:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Filen .model skrives til den angivne mappe. Kan finde oplysninger om denne demo eksempel [på GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Se [xgboost dokumentation side](https://xgboost.readthedocs.org/en/latest/)og dens [Github lager](https://github.com/dmlc/xgboost)kan finde flere oplysninger om xgboost.

#### <a name="rattle"></a>Babyrangle
Babyrangle ( **R** **A**nalytical **T**ool **T**o **L**tjene **E**asily) bruger grafiske brugergrænseflade-baserede data udforske og modellering. Der vises statistiske og visuelle opsummeringer af data, transformationer af data, der kan dannes let, opbygger både fungerer og tilsyn modeller fra dataene, præsenterer ydeevnen for modeller grafisk, og sæt af pointtal, der er nye data. Den genererer også R kode, replikering handlinger i Brugergrænsefladen, som kan køre direkte i R eller bruges som udgangspunkt til yderligere analyse.

Hvis du vil køre Babyrangle, skal du være i et grafisk Fjernskrivebord logon-session. I terminal skal du skrive ```R``` til at angive R-miljøet. Angiv følgende kommandoer, når du bliver spurgt R:

    library(rattle)
    rattle()

Nu åbnes en grafisk grænseflade med et sæt faner. Her er trinnene Hurtig start i Babyrangle, der er behov for at bruge datasættet eksempel vejrudsigter og opbygge en model. I nogle af nedenstående trin, du bliver bedt om at installere og indlæse nogle nødvendige R-pakker, ikke der allerede findes på systemet automatisk.

>[AZURE.NOTE] Hvis du ikke har adgang til at installere pakken i systembiblioteket (standard), kan du se en meddelelse om din R console-vinduet til at installere pakker til dit personlige bibliotek. Besvar *y* , hvis du får vist disse meddelelser.

1. Klik på **Udfør**.
2. En dialogboks vises, hvor du bliver spurgt, hvis du gerne bruge eksempel vejrudsigt datasættet. Klik på **Ja** for at indlæse eksemplet.
3. Klik på fanen **Model** .
4. Klik på **Udfør** for at oprette et beslutningstræ.
5. Klik på **tegn** for at få vist beslutningstræ.
6. Klik på alternativknappen **skov** , og klik på **Udfør** for at opbygge et tilfældigt område.
7. Klik på fanen **Evaluer** .
8. Klik på alternativknappen **risiko** , og klik på **Udfør** for at få vist to områder af risikoen (Cumulative) ydeevne.
9. Klik på fanen **Log** for at få vist Generer R-koden for foregående handlinger.
(På grund af en fejl i den aktuelle udgave af Babyrangle, du vil indsætte en *#* tegnet foran *eksportere denne log …* i teksten i linket log.)
10. Klik på knappen **Eksporter** for at gemme filen R script med navnet *weather_script. R* til mappen privat.

Du kan afslutte Babyrangle og R. Nu kan du ændre scriptet genererede R, eller du kan bruge den, som det er at køre det når som helst for at gentage alt, der blev foretaget i Brugergrænsefladen Rattle. Især for begyndere i R er dette en nem måde kan du hurtigt gøre analyse og machine learning i en enkelt grafiske brugergrænseflade, mens du genererer automatisk kode i R for at ændre og/eller få mere at vide.


## <a name="next-steps"></a>Næste trin
Her er, hvordan du kan fortsætte med din læring og udforske:

* [Data videnskabelige på Linux Data videnskabelige Virtual Machine](machine-learning-data-science-linux-dsvm-walkthrough.md) gennemgang viser, hvordan du udføre mange almindelige data videnskabelige opgaver med Linux Data videnskabelige VM klargjort her. 
* Undersøg de forskellige data videnskabelige værktøjer på data videnskabelige VM ved prøver de værktøjer, der er beskrevet i denne artikel. Du kan også køre *dsvm-mere-oplysninger* på shell inden for den virtuelle maskine til en grundlæggende introduktion og markører til flere oplysninger om de værktøjer, der er installeret på VM.  
* Lær, hvordan du opretter til slut analytical løsninger systematisk ved hjælp af [Team Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Besøg [Cortana Analytics galleriet](http://gallery.cortanaanalytics.com) til maskine læring og data analytics eksempler, der bruger Cortana Analytics-pakke.
