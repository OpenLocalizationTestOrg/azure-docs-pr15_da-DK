<properties
    pageTitle="Scenarier for avancerede analyser proces og teknologi i Azure Machine Learning | Microsoft Azure"
    description="Vælg de relevante scenarier for gøre avancerede skønnet analytics med Team Data videnskabelige processen."
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
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenarier, hvor avanceret analyse i Azure Machine Learning

I denne artikel beskrives forskellige eksempel på datakilder og mål scenarier, der kan løses ved [Team Data videnskabelige proces (TDSP)](data-science-process-overview.md). TDSP indeholder en systematisk fremgangsmåde til at samarbejde på at opbygge intelligente programmer. Scenarier præsenteres her illustrere tilgængelige i arbejdsprocessen databehandling indstillinger, der er afhænger af data egenskaber, kildeplaceringer og typer lagre mål i Azure.

**Beslutningstræ** til at markere eksempelscenarier, der passer til dine data og mål vises i det sidste afsnit.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Hver af de følgende afsnit viser et eksempel på et scenario. For hvert scenario, en mulige data videnskabelige eller avanceret analyse flow og understøttende Azure ressourcer er angivet.

>[AZURE.NOTE]**i alle af følgende scenarier, skal du:**
><br/>
>* [Oprette en lagerplads-konto](../storage/storage-create-storage-account.md)
><br/>
>* [Oprette et Azure Machine Learning arbejdsområde](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Scenarie \#1: små til mellemstore tabelformat dataset i en lokale filer

![Små til mellemstore lokale filer][1]

#### <a name="additional-azure-resources-none"></a>Yderligere Azure ressourcer: ingen

1.  Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

2.  Overføre et datasæt.

3.  Oprette en Azure Machine Learning forsøg rutediagram, der starter med overførte datasæt.

## <a name="smalllocalprocess"></a>Scenarie \#2: små til mellemstore dataset af lokale filer, der kræver behandling

![Små til mellemstore lokale filer med behandling][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure virtuelt (IPython notesbog server)

1.  Oprette en Azure virtuelt kører IPython notesbog.

2.  Overføre data til en objektbeholder til Azure lagring.

3.  Udfyldt behandle og rydde data i IPython notesbog, få adgang til data fra Azure lagerplads beholder.

4.  Transformere data, der skal ryddes, tabelform.

5.  Gemme transformerede data i Azure BLOB.

6.  Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

7.  Læse dataene fra Azure BLOB ved hjælp af [Importdata] [ import-data] modul.

8. Oprette en Azure Machine Learning forsøg rutediagram, der starter med oralt datasæt.

## <a name="largelocal"></a>Scenarie \#3: store datasæt af lokale filer, målretning af Azure BLOB

![Store lokale filer][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure virtuelt (IPython notesbog server)

1.  Oprette en Azure virtuelt kører IPython notesbog.

2.  Overføre data til en objektbeholder til Azure lagring.

3.  Udfyldt procesfarver og Ryd data i IPython notesbog, få adgang til data fra Azure BLOB.

4.  Transformere data, der skal ryddes, tabuleret form, hvis det er nødvendigt.

5.  Udforske data, og Opret funktioner efter behov.

6.  Udtrække en stikprøve af små og mellemstore data.

7.  Gemme de indsamlede data i Azure BLOB.

8. Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

9. Læse dataene fra Azure BLOB ved hjælp af [Importdata] [ import-data] modul.

10. Opbygge Azure Machine Learning forsøg flow starter med oralt datasæt.


## <a name="smalllocaltodb"></a>Scenarie \#4: små til mellemstore dataset af lokale filer, målretning af SQL Server på en Azure Virtal maskine

![Små til mellemstore lokale filer til SQL DB i Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure virtuelt (SQL Server / IPython notesbog server)

1.  Oprette en Azure virtuel maskine, der kører SQL Server + IPython notesbog.

2.  Overføre data til en objektbeholder til Azure lagring.

3.  Udfyldt behandle og rydde data i objektbeholderen til Azure lagring ved hjælp af IPython notesbog.

4.  Transformere data, der skal ryddes, tabelform, hvis det er nødvendigt.

5.  Gemme data til VM-local-filer (IPython notesbog, der kører på VM, lokale drev refererer til VM drev).

6.  Indlæse data til SQL Server-database, der kører på en Azure VM.

    Indstillingen \#1: Brug af SQL Server Management Studio.

    - Logge på SQL Server VM
    - Kør SQL Server Management Studio.
    - Oprette tabeller på database- og destinationswebsteder.
    - Brug en af flere importere metoder til at indlæse dataene fra VM lokale filer.

    Indstillingen \#2: Brug af IPython notesbog – ikke hensigtsmæssigt, mellemstore og store datasæt<!-- -->    
    - Brug ODBC-forbindelsesstreng til at få adgang til SQL Server på VM.
    - Oprette tabeller på database- og destinationswebsteder.
    - Brug en af flere importere metoder til at indlæse dataene fra VM lokale filer.

7.  Udforske data, oprette funktioner efter behov. Bemærk, at funktionerne ikke er nødvendigt at være indtruffet i tabellerne i databasen. Kun Bemærk forespørgslen det er nødvendigt for at oprette dem.

8. Fastlægge en data stikprøvestørrelsen, hvis det er nødvendigt og/eller behov.

9. Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

10. Læse dataene direkte fra SQL Server ved hjælp af [Importdata] [ import-data] modul. Indsætte det nødvendige forespørgsel som henter felter, opretter funktioner og indsamler data, hvis det er nødvendigt direkte i [Importdata] [ import-data] forespørgsel.

11. Opbygge Azure Machine Learning forsøg flow starter med oralt datasæt.

## <a name="largelocaltodb"></a>Scenarie \#5: store datasæt i en lokale filer målrette SQL Server på Azure VM

![Store lokale filer til SQL DB i Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure virtuelt (SQL Server / IPython notesbog server)

1.  Oprette en Azure virtuel maskine, der kører SQL Server og IPython notesbog server.

2.  Overføre data til en objektbeholder til Azure lagring.

3.  (Valgfrit) Udfyldt procesfarver og Ryd data.

    en.  Udfyldt procesfarver og Ryd data i IPython notesbog, få adgang til data fra Azure BLOB.

    b.  Transformere data, der skal ryddes, tabelform, hvis det er nødvendigt.

    c.  Gemme data til VM-local-filer (IPython notesbog, der kører på VM, lokale drev refererer til VM drev).

4.  Indlæse data til SQL Server-database, der kører på en Azure VM.

    en.  Log på SQL Server VM.

    b.  Hvis dataene ikke gemt allerede, kan du hente datafiler fra Azure lagerplads objektbeholder til lokal VM mappe.

    c.  Kør SQL Server Management Studio.

    d.  Oprette tabeller på database- og destinationswebsteder.

    e.  Brug en af flere importere metoder til at indlæse dataene.

    f.  Hvis tabellen joinforbindelser er påkrævet, skal du oprette indeks for at fremskynde joinforbindelser.

     > [AZURE.NOTE] For hurtigere indlæsning af store datastørrelser anbefales det, at du opretter partitioneret tabeller og flere importere dataene parallelt. Du kan finde yderligere oplysninger finder [Parallelle Import af Data til SQL opdelt tabeller](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Udforske data, oprette funktioner efter behov. Bemærk, at funktionerne ikke er nødvendigt at være indtruffet i tabellerne i databasen. Kun Bemærk forespørgslen det er nødvendigt for at oprette dem.

6.  Fastlægge en data stikprøvestørrelsen, hvis det er nødvendigt og/eller behov.

7.  Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

8. Læse dataene direkte fra SQL Server ved hjælp af [Importdata] [ import-data] modul. Indsætte det nødvendige forespørgsel som henter felter, opretter funktioner og indsamler data, hvis det er nødvendigt direkte i [Importdata] [ import-data] forespørgsel.

9. Enkel Azure Machine Learning forsøg flow starter med overførte datasæt

## <a name="largedbtodb"></a>Scenarie \#6: store datasæt i en SQL Server-database lokalt, målretning af SQL Server i en Azure Virtual Machine

![Store SQL DB lokalt til SQL DB i Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure virtuelt (SQL Server / IPython notesbog server)

1.  Oprette en Azure virtuel maskine, der kører SQL Server og IPython notesbog server.

2.  Brug en af dataene eksportere metoder til at eksportere data fra SQL Server til lagring af filer.

    > [AZURE.NOTE] Hvis du beslutter at flytte alle data fra databasens lokalt, en alternativ (hurtigere) metode til at flytte den komplette database til SQL Server-forekomsten i Azure. Gå videre trin for at eksportere data, oprette databasen, og Indlæs/importere data til destinationsdatabasen og følge den alternative metode.

3.  Upload lagring af filer til objektbeholderen til Azure lagring.

4.  Indlæse dataene til en SQL Server-database, der kører på en Azure virtuel maskine.

    en.  Log på SQL Server VM.

    b.  Hent datafiler fra en objektbeholder til Azure lagring til mappen lokale VM.

    c.  Kør SQL Server Management Studio.

    d.  Oprette tabeller på database- og destinationswebsteder.

    e.  Brug en af flere importere metoder til at indlæse dataene.

    f.  Hvis tabellen joinforbindelser er påkrævet, skal du oprette indeks for at fremskynde joinforbindelser.

    > [AZURE.NOTE] For hurtigere indlæsning af store datastørrelser, oprette partitioneret tabeller og til flere importere dataene parallelt. Du kan finde yderligere oplysninger finder [Parallelle Import af Data til SQL opdelt tabeller](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Udforske data, oprette funktioner efter behov. Bemærk, at funktionerne ikke er nødvendigt at være indtruffet i tabellerne i databasen. Kun Bemærk forespørgslen det er nødvendigt for at oprette dem.

6.  Fastlægge en data stikprøvestørrelsen, hvis det er nødvendigt og/eller behov.

7.  Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

8. Læse dataene direkte fra SQL Server ved hjælp af [Importdata] [ import-data] modul. Indsætte det nødvendige forespørgsel som henter felter, opretter funktioner og indsamler data, hvis det er nødvendigt direkte i [Importdata] [ import-data] forespørgsel.

9. Enkel Azure Machine Learning eksperimentere rutediagram, der starter med overførte datasæt.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativ metode til at kopiere en fuld database fra en lokal SQL Server til Azure SQL-Database

![Fjerne lokale DB og vedhæfte til SQL DB i Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure virtuelt (SQL Server / IPython notesbog server)

Hvis du vil gentage hele SQL Server-databasen i dine SQL Server VM, skal du kopiere en database fra én placering/server til en anden, hvis databasen kan overføres til midlertidigt offlinetilstand. Du kan gøre dette i SQL Server Management Studio Object Explorer eller ved hjælp af kommandoerne tilsvarende Transact-SQL.

1. Afbryde forbindelsen til databasen på billedets kildeplacering. Du kan finde flere oplysninger [Afbryd en database](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Kopiere frakoblet databasefil eller filer og logfil eller filer til placeringen på den SQL Server VM i Azure i Windows Stifinder eller Windows-kommandoprompt-vinduet.
3. Vedhæfte de kopierede filer til destinationen SQL Server-forekomsten. Du kan finde yderligere oplysninger finder [Vedhæft en Database](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Flytte en Database ved hjælp af fjerne og vedhæfte (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Scenarie \#7: Big data i lokale filer målrette Hive database i Azure HDInsight Hadoop klynger

![Stor data i lokalt mål Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Yderligere Azure ressourcer: Azure HDInsight Hadoop klynge og Azure Virtual Machine (IPython notesbog server)

1.  Oprette en Azure virtuelt kører IPython notesbog server.

2.  Oprette en Azure HDInsight Hadoop-klynge.

3.  (Valgfrit) Udfyldt procesfarver og Ryd data.

    en.  Udfyldt procesfarver og Ryd data i IPython notesbog, få adgang til data fra Azure BLOB.

    b.  Transformere data, der skal ryddes, tabuleret form, hvis det er nødvendigt.

    c.  Gemme data til VM-local-filer (IPython notesbog, der kører på VM, lokale drev refererer til VM drev).

4.  Overføre data til objektbeholderen standard af den Hadoop klynge valgte i trin 2.

5.  Indlæse data til Hive-database i Azure HDInsight Hadoop klynge.

    en.  Log på noden hoved af Hadoop-klynge

    b.  Åbn Hadoop-kommandolinjen.

    c.  Angive rodmappen Hive kommandoen `cd %hive_home%\bin` i Hadoop-kommandolinjen.

    d.  Køre Hive forespørgsler for at oprette databasen og tabeller, og Indlæs data fra blob-lager til Hive tabeller.

    > [AZURE.NOTE] Hvis dataene er stor, kan brugerne oprette tabellen Hive med partitioner. Derefter brugere kan bruge en `for` løkke i Hadoop kommandolinjen på noden hoved indlæse data til tabellen Hive opdelt ved partition.

6.  Udforske data og oprette funktioner efter behov i Hadoop-kommandolinjen. Bemærk, at funktionerne ikke er nødvendigt at være indtruffet i tabellerne i databasen. Kun Bemærk forespørgslen det er nødvendigt for at oprette dem.

    en.  Log på noden hoved af Hadoop-klynge

    b.  Åbn Hadoop-kommandolinjen.

    c.  Angive rodmappen Hive kommandoen `cd %hive_home%\bin` i Hadoop-kommandolinjen.

    d.  Køre Hive-forespørgsler i Hadoop kommandolinjen på noden hoved af Hadoop-klynge for at udforske data og oprette funktioner efter behov.

7.  Hvis det er nødvendigt, og/eller beskedteksten, eksempel på dataene til at passe ind i Azure Machine Learning Studio.

8.  Log på den [Azure maskine læ Studio](https://studio.azureml.net/).

9. Læse data direkte fra den `Hive Queries` ved hjælp af [Importdata] [ import-data] modul. Indsætte det nødvendige forespørgsel som henter felter, opretter funktioner og indsamler data, hvis det er nødvendigt direkte i [Importdata] [ import-data] forespørgsel.

10. Enkel Azure Machine Learning eksperimentere rutediagram, der starter med overførte datasæt.

## <a name="decisiontree"></a>Beslutningstræ for scenarie markerede
------------------------

I følgende diagram vises de scenarier, der er beskrevet ovenfor og avancerede Analytics proces og teknologi valgmuligheder, der er foretaget, fører til hver af de detaljerede scenarier. Bemærk, at databehandling, udforske, funktion teknisk og stikprøver, hvor der kan tage placere i en eller flere metode/miljø – på den kilde, mellemliggende, og/eller target miljøer – og kan fortsætte flere gange efter behov. Diagrammet kun fungerer som en illustration af nogle af mulige flyder og giver ikke en omfattende optælling.

![DS proces gennemgang eksempelscenarier][8]

### <a name="advanced-analytics-in-action-examples"></a>Avanceret analyse i aktion eksempler

Til slut Azure Machine Learning gennemgange, der benytter den avancerede Analytics proces og teknologi ved hjælp af offentlige datasæt, i:


* [Team Data videnskabelige proces i praksis: Brug af SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Team Data videnskabelige proces i praksis: Brug af HDInsight Hadoop klynger](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
