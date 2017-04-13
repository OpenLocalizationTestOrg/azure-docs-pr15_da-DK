<properties 
    pageTitle="Klargøre Microsoft Data videnskabelige Virtual Machine | Microsoft Azure" 
    description="Konfigurere og oprette en Data videnskabelige virtuel maskine på Azure kan du gøre analyser og machine learning." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Klargøre Microsoft Data videnskabelige Virtual Machine

Microsoft Data videnskabelige Virtual Machine er et Azure VM (virtual machine) billede allerede er installeret og konfigureret med flere populære værktøjer, der ofte bruges til analyse af data og maskine læring. Værktøjerne er:

- Microsoft R Server Developer Edition
- Anaconda Python fordeling
- Jupyter notesbog (med R, Python kerner)
- Visual Studio Community Edition
- Power BI desktop
- SQL Server 2016 Developer Edition
- Learning værktøjsmaskiner
    - [Computational netværk værktøjskassen (CNTK)](https://github.com/Microsoft/CNTK): en deep læ software værktøjskassen fra Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): en hurtig maskine læ filsystem, der understøtter teknikker såsom online, hashing-, allreduce, reduktion, learning2search, aktiv, og interaktive læring.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): et værktøj, give hurtige og præcise øget træet implementering.
    - [Rattle](http://rattle.togaware.com/) (den R Analytical værktøj til at få mere at vide nemt): et værktøj, der gør komme i gang med data analyser og machine learning i R nemt med undersøgelse af grafiske brugergrænseflade-baserede data, og -modellering med automatisk oprettelse af R kode.
    - [mxnet](https://github.com/dmlc/mxnet): en deep learning ramme, der er udviklet til både effektivitet og fleksibilitet
- Biblioteker i R og Python til brug i Azure maskine læring og andre Azure tjenester
- Ciffer, herunder ciffer fest til at arbejde med kilde kode typer lagre herunder GitHub, Visual Studio Team Services
- Windows-porte af flere populære Linux funktioner fra en kommandolinje (herunder awk sed perl, grep, find, wget, og krøllet osv) tilgængelig via kommandoprompt. 


At gøre data videnskabelige omfatter gentagelse på en række opgaver: at finde, indlæse og forbehandling data opbygning og test af modeller og implementering af modeller til forbrug i intelligente programmer. Data forskere bruge en række værktøjer til at fuldføre disse opgaver. Det kan være meget lang tid at finde de relevante versioner af softwaren, og derefter kan du hente og installere dem. Microsoft Data videnskabelige Virtual Machine kan lette denne arbejdsbyrde ved at give et klar til brug billede, der kan være klargjort på Azure alle flere populære værktøjer allerede er installeret og konfigureret. 

Microsoft Data videnskabelige Virtual Machine jump-starts projektet analyser. Det gør det muligt at arbejde på opgaver på forskellige sprog, herunder R, Python, SQL og C#. Visual Studio indeholder en IDE at udvikle og teste din kode, der er nem at bruge. Azure SDK inkluderet i VM giver dig mulighed at opbygge din programmer, der bruger forskellige tjenester på Microsofts skyen platform. 

Der er ingen software gebyrer for dette data videnskabelige VM billede. Du betaler kun for Azure Brugsgebyrer hvilke afhængigt af størrelsen på den virtuelle maskine du klargør. Flere oplysninger om Beregn gebyrerne kan findes i afsnittet priser details på siden [Data videnskabelige Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Forudsætninger

Før du kan oprette en Microsoft Data videnskabelige virtuel maskine, skal du have følgende:

- **En Azure-abonnement**: for at få et, skal du se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **En Azure-lager konto**: for at oprette en, under [oprette en Azure-lager-konto](storage-create-storage-account.md#create-a-storage-account). Du kan også kan kontoen lagerplads oprettes som en del af processen med at oprette VM, hvis du ikke vil bruge en eksisterende konto.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Oprette din Microsoft Data videnskabelige virtuelle maskine

Her er trinnene for at oprette en forekomst af Microsoft Data videnskabelige Virtual Machine:

1.  Gå til den virtuelle maskine listen over på [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Vælg knappen **Opret** nederst tages en guide. ![konfigurere-data-videnskabelige-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   Guiden bruges til at oprette Microsoft Data videnskabelige Virtual Machine kræver **input** for hver af de **fem trin** optælles i højre side af denne figur. Her er de materialer, der er behov for at konfigurere hvert af disse trin:
    
    1.   **Grundlæggende funktioner**
        1.   **Navn**: navnet på din data videnskabelige server, du opretter.
        2.   **Brugernavn**: Admin konto logon-id.
        3.   **Adgangskode**: konto administratoradgangskode.
        4.   **Abonnement**: Hvis du har mere end ét abonnement, kan du vælge den, som computeren er oprettet og faktureret.
        5.   **Ressourcegruppe**: Du kan oprette en ny eller bruge en eksisterende gruppe.
        6.   **Placering**: Vælg datacenteret, der er mest relevante. Det er normalt datacenteret, der indeholder de fleste af dine data eller er tættest på din fysiske placering til hurtigste netværksadgang.
         
    2.   **Størrelse**: Vælg en af de server-datatyper, der opfylder dine funktionelle krav og omkostningsbegrænsninger. Du kan få flere valgmuligheder i VM størrelser ved at vælge "Vis alle".
    
    3.   **Indstillinger**:
        1.   **Disken Type**: Vælg Premium Hvis du foretrækker en solid state drev (SSD), ellers Vælg "Standard".
        2.   **Lagerplads konto**: Du kan oprette en ny firmapost Azure-lager i dit abonnement eller bruge en eksisterende, på samme *placering* , som blev valgt på det **grundlæggende** trin i guiden.
        3.   **Andre parametre**: normalt du kan bruge standardværdierne. Hvis du vil overveje brugen af ikke-standardværdier, kan du pege på linket informativ for hjælp til de specifikke felter.

    4.   **Oversigt**: Bekræft, at alle oplysninger, du har angivet er korrekte.
    5.   **Købe**: Klik på **Køb** til at starte klargøringen. Et link, der overføres til vilkårene for posteringen. VM har ikke eventuelle yderligere gebyrer ud over Beregn til den server størrelse, du vælger i trinnet **størrelse** . 


>[AZURE.NOTE] Klargøringen fører omkring 10-20 minutter. Status for klargøringen vises på Azure-portalen.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Hvordan du kan få adgang til Microsoft Data videnskabelige Virtual Machine

Når VM er oprettet, kan du Fjernskrivebord til den ved hjælp af konto Administratoroplysninger, du konfigurerede i det forrige **grundlæggende** afsnit. 

Når din VM er oprettet og klargjort, er du klar til at begynde at bruge de værktøjer, der er installeret og konfigureret på den. Der er start menuen felter og ikoner på skrivebordet for mange af værktøjerne. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Sådan oprettes en stærk adgangskode på Jupyter notesbog serveren 

Hvis du vil oprette dine egne stærk adgangskode for den Jupyter notesbog server, der er installeret på computeren, skal du køre følgende kommando fra en kommandoprompt på den Data videnskabelige virtuelle maskine.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Vælg en stærk adgangskode, når du bliver bedt om.

Du kan se adgangskodehash-værdien i formatet "sha1:xxxxxx" i outputtet. Kopiér denne adgangskode hash og erstatte den eksisterende hashværdi, der er i din notesbog konfigurationsfil adressen: **C:\ProgramData\jupyter\jupyter_notebook_config.py** med en parameter navnet ***c.NotebookApp.password***.

Erstat kun (xxxxxx) del af den eksisterende hash-værdi, der er i anførselstegn. Anførselstegn og ***sha1:*** præfiks for den værdi for parameteren begge skal bevares.

Til sidst skal du har brug at afslutte og genstarte Jupyter server, der kører på VM som en windows planlagt opgave kaldet **Start_IPython_Notebook**. Hvis din nye adgangskode ikke er accepteret når du har genstartet denne opgave, kan du prøve aflivning alle kørende python processer fra Jobliste og derefter genstarte den planlagte opgave. Alternativt kan du prøve at genstarte den virtuelle maskine.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Funktioner, der er installeret på Microsoft Data videnskabelige Virtual Machine

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Hvis du vil bruge R til din analytics, har VM Microsoft R Server Developer edition er installeret. Microsoft R Server er et bredt installeres professionel analytics platform baseret på R, der understøttes, SVG og sikker. Understøttelse af en række forskellige stor data statistik, skønnet modellering og machine learning-funktioner, understøtter R Server et bredt udvalg af analyser – udforske, analyse, visualisering og modellering. Ved at bruge, og hvis du vil udvide Åbn kilde R, er Microsoft R Server fuldt kompatibelt med R scripts, funktioner og CRAN-pakker til at analysere data skaleres til enterprise. Det løser også åbne kilde R i hukommelsen begrænsninger ved at tilføje parallelle og chunked behandling af data. Dette gør det muligt at køre analyse af data, der er meget større end hvad passer ind i den primære hukommelse.  Visual Studio Community Edition inkluderet på VM indeholder R-værktøjer til Visual Studio-typenavn, der indeholder en fuld IDE til at arbejde med R. Du kan også hente og bruge andre IDEs samt som [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Til udvikling ved hjælp af Python, er Anaconda Python fordeling 2.7 og 3.5 installeret. Denne fordeling indeholder grundlæggende Python sammen med om 300 af de mest populære matematisk, teknisk og data analytics pakker. Du kan bruge Python værktøjer til Visual Studio (PTVS), der er installeret i Visual Studio 2015 Community edition eller en af de IDEs, sammen med Anaconda som inaktiv eller Spyder. Du kan starte en af disse ved at søge på søgepanelet (**vinder** + **S** nøgle).

>[AZURE.NOTE] Hvis du vil pege Python værktøjerne til Visual Studio på Anaconda Python 2.7 og 3.5, skal du oprette brugerdefinerede miljøer til hver version. Hvis du vil angive disse miljø stier i Visual Studio 2015 Community Edition, skal du gå til **værktøjer** -> **Python værktøjer** -> **Python miljøer** og derefter klikke på **+ brugerdefineret**. 

Anaconda Python 2.7 installeres under C:\Anaconda og Anaconda Python 3.5 er installeret under c:\Anaconda\envs\py35. Dokumentationen [PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) have en detaljeret vejledning. 

### <a name="jupyter-notebook"></a>Jupyter notesbog
Anaconda fordeling leveres også med en Jupyter notesbog, et miljø til at dele kode og analyse. En Jupyter notesbog server er allerede konfigureret med Python 2, 3 Python og R kerner. Der er et ikon på skrivebordet med navnet "Jupyter notesbog for at starte browseren for at få adgang til notesbog-serveren. Hvis du er på VM via Fjernskrivebord, du kan også besøge [https://localhost:9999 /](https://localhost:9999/) til at få adgang til Jupyter notesbog serveren når logget på til VM.
 
>[AZURE.NOTE] Fortsæt, hvis du bliver advaret et certifikat. 

Vi har pakket eksempel notesbøger i Python og i R. Jupyter notesbøger viser, hvordan du arbejder med Microsoft R Server, SQL Server 2016 R Services (i databasen analytics), Python og andre teknologier til Azure, når du logger på Jupyter. Du kan se linket til eksemplerne på hjemmesiden notesbog efter at du godkende Jupyter notesbogen med den adgangskode, du har oprettet i et tidligere trin. 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community edition
Visual Studio Community edition er installeret på VM. Det er en gratis version af de populære IDE fra Microsoft, som du kan bruge til evalueringsformål og for små grupper. Du kan se licensvilkårene [her](https://www.visualstudio.com/support/legal/mt171547).  Åbne Visual Studio ved at dobbeltklikke på ikonet på skrivebordet eller menuen **Start** . Du kan også søge efter programmer med **vinder** + **S** og indtaste "Visual Studio". Når der kan du oprette projekter i sprog som C#, Python, R, node.js. Plug-ins er også er installeret, der gør det nemt at arbejde med Azure tjenester som Azure datakatalog, Azure HDInsight (Hadoop, gnister) og Azure Data sø. 

>[AZURE.NOTE] Du kan få en meddelelse om, at evalueringen er udløbet. Angiv dine Microsoft-kontolegitimationsoplysninger, eller Opret en ny gratis konto for at få adgang til Visual Studio Community Edition. 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer edition
En udvikler version af SQL Server 2016 med R-tjenester til at køre i databasen analytics er angivet i VM. R tjenester giver en platform til udvikling af og implementere intelligente programmer. Du kan bruge de mange og effektive R sprog og de mange pakker fra community'et til at oprette modeller og generere prognoser for dine SQL Server-data. Du kan holde analytics tæt ved dataene, fordi R tjenester (i-database) integrere R sprog med SQL Server. Dette fjerner omkostninger og sikkerhedsrisici, der er knyttet til flytning af data.

>[AZURE.NOTE] SQL Server 2016 developer edition kan kun bruges til udvikling og testformål. Du skal have en licens til at køre i fremstilling. 

Du kan få adgang til SQL server ved at starte **SQL Server Management Studio**. Navnet på din VM udfyldes som navnet på serveren. Brug Windows-godkendelse når logget på som administrator i Windows. Når du befinder dig i SQL Server Management Studio kan du oprette andre brugere, oprette databaser, importere data og køre SQL-forespørgsler. 

Kør følgende kommando for at aktivere i databasen analyser ved hjælp af Microsoft R, som en tid handling i SQL Server management studio efter logføring i som serveradministratoren. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Flere Azure værktøjerne er installeret på VM:

- Der findes en skrivebordsgenvej til at få adgang til dokumentationen til Azure SDK. 
- **AzCopy**: bruges til at flytte data og kontoen Microsoft Azure-lager. For at se brugen skal du skrive **Azcopy** ved en kommandoprompt til at se, hvordan du kan bruge. 
- **Microsoft Azure lagerplads Explorer**: bruges til at gennemse de objekter, du har gemt i din Azure lagerplads konto og overføre data til og fra Azure-lager. Du kan skrive **Storage Explorer** i Søg eller finde det i menuen Start i Windows for at få adgang til dette værktøj. 
- **Adlcopy**: bruges til at flytte data til Azure Data sø. For at se brugen skal du skrive **adlcopy** i en kommandoprompt. 
- **dtui**: bruges til at flytte data til og fra Azure DocumentDB, en NoSQL database i skyen. Skriv **dtui** på kommandoprompt. 
- **Microsoft Data Management Gateway**: gør det muligt for flytning af data mellem lokale datakilder og skyen. Det bruges i værktøjer som Azure Data Factory. 
- **Microsoft Azure Powershell**: et værktøj, der bruges til at administrere dine Azure ressourcer i Powershell scriptsproget også er installeret på din VM. 

###<a name="power-bi"></a>Power BI

For at hjælpe dig med at opbygge dashboards og gode visualiseringer, er blevet installeret **Power BI Desktop** . Brug dette værktøj til at hente data fra forskellige kilder til at redigere dine dashboards og -rapporter samt til at publicere dem til skyen. Du kan finde oplysninger, [Power BI](http://powerbi.microsoft.com) -webstedet. Du kan finde Power BI desktop i menuen Start. 

>[AZURE.NOTE] Du har brug for en Office 365-konto for at få adgang til Power BI. 

## <a name="additional-microsoft-development-tools"></a>Flere Microsoft development-værktøjer
[**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) kan bruges til at finde og hente andre værktøjer til udvikling af Microsoft. Der er også en genvej til det værktøj, der findes på Microsoft Data videnskabelige Virtual Machine skrivebordet.  

## <a name="important-directories-on-the-vm"></a>Vigtige mapper på VM

| Element                          | Directory |
| ------------------------------| ---------------- |
|Jupyter notesbog serverkonfigurationer | C:\ProgramData\jupyter |
|Jupyter notesbog eksempler eget bibliotek| c:\dsvm\notebooks|
|Andre eksempler | c:\dsvm\samples|
| Anaconda (standard: Python 2.7) | c:\Anaconda |
| Anaconda Python 3.5 miljø | c:\Anaconda\envs\py35|
|R Server enkeltstående forekomst directory (standard R forekomst) | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| R Server-database forekomst directory | C:\Program Files\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Diverse funktioner | c:\dsvm\tools|

>[AZURE.NOTE] Forekomster af Microsoft Data videnskabelige Virtual Machine oprettet før 1.5.0 (før 2016, September 3) bruges en lidt anden mappestruktur, end der er specificeret i ovenstående tabel. 

## <a name="next-steps"></a>Næste trin
Her er nogle næste trin at fortsætte med din læring og udforske. 

* Udforske de forskellige data videnskabelige værktøjer på data videnskabelige VM ved at klikke på startmenuen og udtjekning af vises i menuen værktøjer.
* Gå til **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** for eksempler ved hjælp af biblioteket RevoScaleR i R, der understøtter data analytics skaleres til enterprise.  
* Læse artiklen: [10 ting du kan udføre på Data videnskabelige virtuelt](http://aka.ms/dsvmtenthings)
* Lær, hvordan du opretter start til slut analytical løsninger systematisk ved hjælp af [Team Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Besøg [Cortana Intelligence galleriet](http://gallery.cortanaintelligence.com) til maskine læring og data analytics eksempler, der bruger Cortana Intelligence pakke. Vi har også angivet et ikon i menuen **Start** og på computeren på den virtuelle maskine til dette galleri.

