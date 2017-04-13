<properties
    pageTitle="Konfigurere en SQL Server virtuel maskine, som en server til IPython notesbog | Microsoft Azure"
    description="Angive op en Data videnskabelige virtuel maskine med SQL Server og IPython Server."
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
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Konfigurere et virtuelt Azure SQL Server som en server til IPython notesbog til avanceret analyse

Dette emne viser, hvordan du klargør og konfigurere en SQL Server virtuelt skal bruges som en del af et skybaseret data videnskabelige miljø. Virtuelt Windows er konfigureret med understøtter funktioner som IPython notesbog, Azure Storage Explorer og AzCopy samt andre funktioner, der er praktiske, når data videnskabelige projekter. Azure-lager Explorer og AzCopy, for eksempel indeholder praktiske funktioner til at overføre data til Azure blob-lager fra din lokale computer eller for at hente den til din lokale computer fra blob-lager.

Galleriet Azure virtuelt indeholder flere billeder, der indeholder Microsoft SQL Server. Vælg et billede i SQL Server VM, der passer til dine data behov. Anbefalede billeder er:

- SQL Server 2012 SP2 Enterprise for små til mellemstore datastørrelser
- SQL Server 2012 SP2 Enterprise optimeret til DataWarehousing arbejdsmængder for stor til meget store datastørrelser

 > [AZURE.NOTE] SQL Server 2012 SP2 Enterprise billede **ikke inkluderer en datadisk**. Du skal tilføje og/eller vedhæfte en eller flere virtuelle harddiske for at gemme dine data. Når du opretter en Azure virtuelt, har en disk til det operativsystem, der er knyttet til C-drevet og en midlertidig disk, der er knyttet til drev D. Brug ikke drev D til at gemme data. Som navnet antyder, indeholder den midlertidige lagerplads. Den indeholder ingen redundans eller sikkerhedskopi, fordi det ikke er placeret i Azure-lager.


##<a name="Provision"></a>Oprette forbindelse til portalen Azure klassisk og klargøre virtuelt en SQL Server

1.  Log på [Azure klassisk Portal](http://manage.windowsazure.com/) ved hjælp af din konto.
    Hvis du ikke har en Azure-konto, kan du besøge [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

2.  På Azure klassisk-portalen, nederst til venstre på websiden, skal du klikke på **+ Ny**, skal du klikke på **beregne**, skal du klikke på **VIRTUELT**og klik derefter på **Fra GALLERIET**.

3.  Vælg et virtuelt billede, der indeholder SQL Server, der er baseret på dine data behov på siden **Opret en virtuel maskine** , og klik derefter på næste pilen nederst til højre på siden. De mest opdaterede oplysninger om de understøttede SQL Server-billeder på Azure emnet [Introduktion til SQL Server på virtuelle Azure-computere](http://go.microsoft.com/fwlink/p/?LinkId=294720) i dokumentationen til [SQL Server på virtuelle Azure-computere](http://go.microsoft.com/fwlink/p/?LinkId=294719) angive.

    ![Vælg SQL Server VM][1]

4.  Angiv følgende oplysninger på siden første **Virtuelt konfiguration** :

    -   Angiv et **VIRTUELT navn**.
    -   Skriv entydige brugernavnet for den lokale administratorkonto VM i feltet **Ny brugernavn** .
    -   Skriv en stærk adgangskode i feltet **Ny adgangskode** . Du kan finde flere oplysninger [Stærke adgangskoder](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Skriv adgangskoden igen i feltet **Bekræft adgangskode** .
    -   Vælg den relevante **størrelse** på rullelisten.

     > [AZURE.NOTE] Størrelsen af den virtuelle maskine er angivet under klargøringen: A2 er den mindste størrelse, der anbefales til fremstilling arbejdsmængder. Den mindste anbefalede størrelse for en virtuel maskine er A3, når du bruger SQL Server Enterprise Edition. Vælg A3 eller nyere, når du bruger SQL Server Enterprise Edition. Vælge A4, når du bruger SQL Server 2012 eller 2014 Enterprise optimeret til transaktions arbejdsbelastninger billeder.
Vælg A7, når du bruger SQL Server 2012 eller 2014 Enterprise optimeret til Data opbevaring arbejdsbelastninger billeder. Den størrelse, der er valgt begrænser antallet af datadisce, kan du konfigurere. Du kan finde mest opdaterede oplysninger om tilgængelige virtuelt størrelser og antallet datadisce, der kan vedhæftes en virtuel maskine, [Virtuelt størrelser til Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Prisoplysninger, se [Virtuelle Macines priser](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Klik på næste pilen nederst til højre til at fortsætte.

    ![VM konfiguration][2]

5.  Konfigurere ressourcer for netværk, storage og tilgængelighed på den anden side i **virtuelt konfiguration** :

    -   Vælg **Opret en ny tjeneste i skyen**, i feltet **Skybaseret tjeneste** .
    -   I feltet **Skyen tjenesten DNS-navn** , angive den første del af en DNS-navnet på dit valg kan, så det afsluttes et navn i formatet **TESTNAME.cloudapp.net**
    -   Vælg et område, hvor denne virtuelle billede skal placeres i feltet **Område/forbindelse gruppe/VIRTUELLE netværk** .
    -   Vælg en eksisterende konto lagerplads i **Lagerplads konto**, eller Vælg en automatisk oprettede.
    -   Vælg **(ingen)**i feltet **Angiv tilgængelighed** .
    -   Læs og Accepter prisoplysninger.

6.  I sektionen **SLUTPUNKTER** skal du klikke på i den tomme rullemenuen under **navn**, og vælg **MSSQL** og skriv derefter portnummeret for forekomsten af Database Engine (**1433** standardforekomst).

7.  Din SQL Server VM kan også fungere som en IPython notesbog Server, der skal konfigureres på et senere tidspunkt.
    Tilføje et nyt slutpunkt for at angive porten, der skal bruges til serverens IPython notesbog. Angiv et navn i kolonnen **navn** skal du vælge et portnummer af dit valg for den offentlige port og 9999 til den private port.

    Klik på næste pilen nederst til højre til at fortsætte.

    ![Vælg MSSQL og IPython porte][3]

8.  Acceptere standardindstillingen **installere VM agent** markeret og klik på den markering i nederste højre hjørne i guiden for at fuldføre VM klargøring af processen.

    `![VM sidste indstillinger][4]

9.  Vent, mens Azure laver din virtuelle maskine. Forvente virtuelt statussen for at fortsætte gennem:

    -   Starter (klargøring)
    -   Ikke længere
    -   Starter (klargøring)
    -   Kører (klargøring)
    -   Kører


##<a name="RemoteDesktop"></a>Åbn den virtuelle maskine ved hjælp af Remote Desktop og fuldføre installationen

1.  Når klargøring er fuldført, klikke på navnet på din virtuelle maskine at gå til siden DASHBOARD. Klik på **Forbind**nederst på siden.

2.  Vælg til at åbne filen rpd ved hjælp af Windows Remote Desktop program (`%windir%\system32\mstsc.exe`).

3.  I dialogboksen **Windows sikkerhed** skal du angive adgangskoden for den lokale administratorkonto, du angav i et tidligere trin.
    (Du kan blive bedt om at bekræfte legitimationsoplysningerne for den virtuelle maskine.)

4.  Første gang du logger på denne virtuelt muligvis flere processer at fuldføre, herunder konfiguration af dit skrivebord, Windows-opdateringer og fuldførelse af Windows indledende konfigurationsopgaver (sysprep). Når Windows sysprep er fuldført, fuldfører installationen af SQL Server konfigurationsopgaver. Disse opgaver kan medføre en forsinkelse på et par minutter, mens de fuldfører. `SELECT @@SERVERNAME`kan ikke returnere det rigtige navn, indtil SQL Server-installationen er fuldført, og SQL Server Management Studio muligvis ikke visable på startsiden.

Når du har forbindelse til den virtuelle maskine med Windows Fjernskrivebord, virker den virtuelle maskine meget som en anden computer. Oprette forbindelse til standardforekomst af SQL Server med SQL Server Management Studio (kører på den virtuelle maskine) på normal vis.


##<a name="InstallIPython"></a>Installere IPython notesbog og andre understøttende værktøjer

For at konfigurere din nye SQL Server VM fungerer som en server til IPython notesbog og installerer ekstra understøttende værktøjer, sådanne AzCopy, Azure Storage Explorer, nyttige Data videnskabelige Python pakker og andre, er angivet en speciel tilpasning script til dig. Sådan installeres:

- Højreklik på ikonet **Start i Windows** og klikke på **kommandoprompt (Admin)**
- Kopiere følgende kommandoer og indsætte ved kommandoprompten.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Når du bliver bedt om det, kan du angive en adgangskode efter eget valg til IPython notesbog-serveren.
- Tilpasning af scriptet automatisere flere efter installationen procedurer, som omfatter:
    + Installation og konfiguration af IPython notesbog server
    + Åbne TCP-porte i Windows firewall til slutpunkterne har oprettet tidligere:
    + For SQL Server remote connectivity
    + For IPython notesbog serverforbindelse remote
    + Henter eksempel IPython notesbøger og SQL-scripts
    + Hente og installere nyttige Data videnskabelige Python-pakker
    + Hente og installere Azure funktioner som AzCopy og Azure Storage Explorer  
<br>
- Du kan få adgang til og køre IPython notesbog fra en hvilken som helst lokale eller en fjerndatabase browseren ved hjælp af en URL-adresse i formularen `https://<virtual_machine_DNS_name>:<port>`, hvor port er IPython offentlige porten, du har valgt under klargøring af den virtuelle maskine.
- IPython notesbog server kører som en tjeneste i baggrunden og skal genstartes automatisk, når du genstarter den virtuelle maskine.

##<a name="Optional"></a>Vedhæft datadisk efter behov

Hvis din VM billede ikke indeholder data disk det vil sige diske end C-drevet (OS disken) og drev D (midlertidige disken), skal du tilføje en eller flere datadisce for at gemme dine data. VM billedet til SQL Server 2012 SP2 Enterprise optimeret til DataWarehousing arbejdsbelastninger leveres forudkonfigurerede med flere diske for SQL Server data og logfiler.

 > [AZURE.NOTE] Brug ikke drev D til at gemme data. Som navnet antyder, indeholder den midlertidige lagerplads. Den indeholder ingen redundans eller sikkerhedskopi, fordi det ikke er placeret i Azure-lager.

Følg trinnene beskrevet i [Sådan Vedhæft en Disk med Data til en Windows virtuel maskine](virtual-machines-windows-classic-attach-disk.md), der hjælper dig gennem for at vedhæfte flere datadisce skal:

1. Vedhæfte tom disk til den virtuelle maskine, der er klargjort i tidligere trin
2. Initialisering af nye disken(e) i virtual machine


##<a name="SSMS"></a>Oprette forbindelse til SQL Server Management Studio og aktivere blandet tilstand-godkendelse

SQL Server-Database Engine kan ikke bruge Windows-godkendelse uden domænemiljø. For at oprette forbindelse til Database Engine fra en anden computer, konfiguration af SQL Server til blandet tilstand-godkendelse. Blandet tilstand-godkendelse kan både SQL Server-godkendelse og Windows-godkendelse. SQL-godkendelsestilstand er påkrævet, at indtager data direkte fra dine SQL Server VM databaser i [Azure Machine Learning Studio](https://studio.azureml.net) bruger modulet importdata.

1.  Mens forbindelse til den virtuelle maskine ved hjælp af Fjernskrivebord, bruge ruden Windows **Search** , og skriv **SQL Server Management Studio** (SMSS). Klik for at starte SQL Server Management Studio (SSMS). Du vil føje en genvej til SSMS på din computer til senere brug.

    ![Starte SSMS][5]

    Første gang du åbner Management Studio skal det oprette brugere Management Studio miljø. Dette kan tage et øjeblik.

2.  Når du åbner, vises dialogboksen **Opret forbindelse til Server** Management Studio. Skriv navnet på den virtuelle maskine til at oprette forbindelse til Database Engine med Object Explorer i feltet **servernavn** .
    (I stedet for navnet på den virtuelle computer du kan også bruge **(lokalt)** eller en enkelt periode som **servernavn**. Vælg **Windows-godkendelse**, og Lad ** *din\_VM\_navn*\\din\_lokale\_administrator** i feltet **brugernavn** . Klik på **Opret forbindelse**.

    ![Oprette forbindelse til Server][6]

    <br>

     > [AZURE.TIP] Du kan ændre den SQL Server-godkendelsestilstand ved hjælp af en Windows-registreringsdatabasen vigtige ændring eller ved hjælp af SQL Server Management Studio. Starte en **Ny forespørgsel** for at ændre godkendelsestilstand ved hjælp af de vigtigste ændring i registreringsdatabasen, og udføre følgende script:

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Sådan ændrer du godkendelsesmetode ved hjælp af SQL Server management Studio:

3.  Højreklik på navnet på forekomsten af SQL Server (virtuelt navnet) i **SQL Server Management Studio Object Explorer**, og klik derefter på **Egenskaber**.

    ![Egenskaber for Server][7]

4.  Vælg **SQL Server og Windows-godkendelse tilstand**på siden **sikkerhed** under **Server-godkendelse**, og klik derefter på **OK**.

    ![Vælg godkendelsestilstand][8]

5.  Klik på **OK** for at acceptere kravet om at genstarte SQL Server i dialogboksen **SQL Server Management Studio** .

6.  Højreklik på din server i **Object Explorer**, og klik derefter på **Genstart**. (Hvis der kører SQL Server Agent, den skal også genstartes.)

    ![Genstart][9]

7.  Klik på **Ja** for at acceptere, du vil genstarte SQL Server i dialogboksen **SQL Server Management Studio** .

##<a name="Logins"></a>Oprette SQL Server-godkendelse logon

Hvis du vil oprette forbindelse til Database Engine fra en anden computer, skal du oprette mindst én SQL Server-godkendelse login.  

Du kan oprette nye SQL Server-logon fra et program eller ved hjælp af SQL Server Management Studio. For at oprette en ny bruger af systemadministrator med SQL-godkendelse fra et program, skal du starte en **Ny forespørgsel** og udføre følgende script. Erstatte < nye brugernavn\> og < nye adgangskode\> med dit valg af *brugernavn* og *din adgangskode*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Juster adgangskodepolitik efter behov (de eksempel kode slukker politik kontrollere og adgangskode udløb). Se [oprette et logon](http://msdn.microsoft.com/library/aa337562.aspx)kan finde flere oplysninger om SQL Server-logon.  

Sådan oprettes nye SQL Server-logon ved hjælp af SQL Server Management Studio:

1.  Udvid mappen på den server-forekomst, hvor du vil oprette den nye login i **SQL Server Management Studio Object Explorer**.

2.  Højreklik på mappen **sikkerhed** , peg på **Ny**og vælge **logon …**.

    ![Nyt logon][10]

3.  Angiv navnet på den nye bruger i feltet **Login-navn** i dialogboksen **Login - ny** på siden **Generelt** .

4.  Vælg **SQL Server-godkendelse**.

5.  Angiv en adgangskode til den nye bruger i feltet **adgangskode** . Skriv adgangskoden igen i feltet **Bekræft adgangskode** .

6.  For at gennemtvinge adgangskode politikindstillinger for kompleksitet og håndhævelse, skal du vælge **Gennemtving adgangskodepolitik** (anbefales). Dette er en standardindstilling, når SQL Server-godkendelse er markeret.

7.  For at gennemtvinge adgangskode politikindstillinger for udløb, skal du vælge **Gennemtving udløb af adgangskoder** (anbefales). Gennemtvinge adgangskodepolitik skal være markeret for at aktivere dette afkrydsningsfelt. Dette er en standardindstilling, når SQL Server-godkendelse er markeret.

8.  For at tvinge brugeren til at oprette en ny adgangskode efter første gang logon bruges, Markér **brugeren skal skifte adgangskode ved næste logon** (anbefales, hvis dette logon er til en anden til at bruge. Hvis logon er til egen brug, Viderestil ikke Markér denne indstilling.) Gennemtvinge udløb af adgangskoder skal være markeret for at aktivere dette afkrydsningsfelt. Dette er en standardindstilling, når SQL Server-godkendelse er markeret.

9.  Vælg en standarddatabase til logon fra listen **standard-database** . **mastersider** er standard for denne indstilling. Hvis du endnu ikke har oprettet en brugerdatabase, kan du lade dette sæt til **master**.

10. Lad være **standard** denne værdi på listen **standardsprog** .

    ![Logonegenskaber for][11]

11. Hvis dette er den første logon, du opretter, kan du angive dette logon som en SQL Server-administratoren. Hvis det er tilfældet, skal du på siden **Serverroller** kontrollere **systemadministrator**.

    > [AZURE.IMPORTANT] Medlemmer af rollen systemadministrator fast server har fuld kontrol over Database Engine. Af sikkerhedsmæssige årsager, skal du omhyggeligt begrænse medlemskab af denne rolle.

    ![systemadministrator][12]

12. Klik på OK.

##<a name="DNS"></a>Finde DNS-navnet på den virtuelle maskine

Hvis du vil oprette forbindelse til SQL Server-Database Engine fra en anden computer, skal du kender DNS Domain Name System () navnet på den virtuelle maskine.

(Dette er navnet på internettet, der bruges til at identificere den virtuelle maskine. Du kan bruge IP-adressen, men IP-adressen kan ændres, når Azure flytter ressourcer til redundans og vedligeholdelse. DNS-navnet, være stabil fordi det kan blive omdirigeret til en ny IP-adresse.)

1.  Vælg **VIRTUELLE maskiner**, i portalen Azure klassisk (eller fra det forrige trin).

2.  På siden **VIRTUAL MACHINE FOREKOMSTER** i kolonnen **DNS-navn** skal du finde og kopiere og indsætte DNS-navnet for den virtuelle maskine, som vises preceded ved **http://**. (Hele navnet ikke kan vises i brugergrænsefladen, men du kan højreklikke på den, og vælg Kopiér).

##<a name="cde"></a>Oprette forbindelse til Database Engine fra en anden computer

1.  Åbn SQL Server Management Studio på en computer, der er forbindelse til internettet.

2.  Angiv DNS-navnet på den virtuelle maskine (bestemmes i den forrige opgave) og et offentligt slutpunkt portnummer af *DNSName, portnummer* i formatet som **tutorialtestVM.cloudapp.net,57500**i dialogboksen **Opret forbindelse til Server** eller **oprette forbindelse til Database Engine** i feltet **servernavn** .

3.  Vælg **SQL Server-godkendelse**i feltet **godkendelse** .

4.  Skriv navnet på et logon, du har oprettet i en tidligere opgave i feltet **Login** .

5.  Skriv adgangskoden til logon, du opretter i en tidligere opgave i feltet **adgangskode** .

6.  Klik på **Opret forbindelse**.

##<a name="amlconnect"></a>Oprette forbindelse til Database Engine fra Azure Machine Learning

Du skal bruge [Azure maskine læ Studio](https://studio.azureml.net) til at opbygge og anvende machine learning modeller på et senere tidspunkt af Team Data videnskabelige processen. Indtager data fra SQL Server VM databaser direkte i Azure Machine Learning til uddannelse eller vundne, bruge modulet **Importere Data** i en ny [Azure Machine Learning Studio](https://studio.azureml.net) forsøg. Dette emne er beskrevet i få mere at vide gennem linkene Team Data videnskabelige proces vejledning. Se en introduktion i [Hvad er Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md).

2.  Vælg **Azure SQL-Database** på rullelisten **Datakilde** i ruden **Egenskaber** i [importdata modul](https://msdn.microsoft.com/library/azure/dn905997.aspx).

3.  Angiv i tekstfeltet **Database servernavn**`tcp:<DNS name of your virtual machine>,1433`

4.  Angive SQL-brugernavn i feltet **Server-konto brugernavn** .

5.  Angiv sql-brugers adgangskode i feltet **Server adgangskode til brugerkonto** .

    ![Azure ML Importér Data][13]

##<a name="shutdown"></a>Lukning og deallokere virtuelt når den ikke er i brug

Azure virtuelle computere er prisen som **betale for kun at du bruger**. For at sikre, at du ikke er blive faktureret når du ikke bruger din virtuelle maskine, skal den være i tilstanden **stoppet (Deallocated)** .

> [AZURE.NOTE] Lukke den virtuelle maskine fra i (ved hjælp af indstillinger for Windows power), er ikke længere VM men forbliver allokeres. For at sikre, at du ikke er blive faktureret, altid stop virtuelle maskiner fra [Azure klassisk Portal](http://manage.windowsazure.com/). Du kan også stoppe VM via Powershell ved at ringe ShutdownRoleOperation med "PostShutdownAction" lig med "StoppedDeallocated".

At lukke og Dealloker den virtuelle maskine:

1. Log på [Azure klassisk Portal](http://manage.windowsazure.com/) ved hjælp af din konto.  

2. Vælg **VIRTUELLE maskiner** venstre navigationslinje.

3. Klik på navnet på din virtuelle maskine på listen over virtuelle maskiner, og gå til siden **DASHBOARD** .

4. Klik på **Luk computeren**nederst på siden.

![VM lukning][15]

Den virtuelle maskine bliver deallokeres, men ikke slettet. Du kan genstarte computeren virtuelle når som helst fra portalen Azure klassisk.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Din Azure SQL Server-VM er klar til brug: Hvad skal der ske nu?

Din virtuelle maskine er nu klar til brug i dine data videnskabelige øvelser. Den virtuelle maskine er også klar til brug som en server til IPython notesbog til udforske og behandling af data og andre opgaver sammen med Azure maskine læring og Team Data videnskabelige proces (TDSP).

De næste trin i processen data videnskabelige er tilknyttet [Team Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) og kan indeholde trin, flytte data til HDInsight, proces, og lyt det der forberedelse til learning fra dataene med Azure maskine læ.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
