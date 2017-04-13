<properties 
    pageTitle="Behandle Data fra SQL Azure | Microsoft Azure" 
    description="Processen Data fra SQL Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Processen Data i SQL Server virtuel maskine på Azure

Dette dokument omhandler, hvordan til at udforske data og generere funktioner for data, der gemmes i en SQL Server VM på Azure. Dette kan gøres ved data wrangling ved hjælp af SQL eller ved hjælp af et programmeringssprog som Python.


> [AZURE.NOTE] Eksempel på SQL-sætningerne i dette dokument forudsætter, at dataene er i SQL Server. Hvis det ikke er, kan du se procesoversigten skyen data videnskabelige lære at flytte dine data til SQL Server.

##<a name="SQL"></a>Ved hjælp af SQL

Vi beskriver de følgende data wrangling opgaver i dette afsnit, ved hjælp af SQL:

1. [Udforske data](#sql-dataexploration)
2. [Generering af funktion](#sql-featuregen)

###<a name="sql-dataexploration"></a>Udforske data
Her er nogle eksempler på SQL-scripts, som kan bruges til at udforske data butikker i SQL Server.


> [AZURE.NOTE] Du kan bruge [NYC Taxi datasæt](http://www.andresmh.com/nyctaxitrips/) og referere til IPNB med titlen [NYC Data wrangling med IPython notesbog og SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) for en til slut gennemgå praktiske f.eks.

1. Få antallet af observationer dagen

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Få niveauer i en kategoriske kolonne

    `select  distinct <column_name> from <databasename>`

3. Få antallet niveauer i kombination af to kategoriske kolonner 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Få en fordeling for numeriske kolonner

    `select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Generering af funktion

I dette afsnit beskriver vi måder til oprettelse af funktioner, der bruger SQL:  

1. [Tælle baseret generering af funktion](#sql-countfeature)
2. [Generering af binning funktion](#sql-binningfeature)
3. [Rullende funktioner fra en enkelt kolonne](#sql-featurerollout)


> [AZURE.NOTE] Når du opretter yderligere funktioner, kan du tilføje dem som kolonner til den eksisterende tabel eller oprette en ny tabel med yderligere funktioner og primær nøgle, der kan sammenkædes med den oprindelige tabel. 

###<a name="sql-countfeature"></a>Tælle baseret generering af funktion

Dette dokument viser to måder til oprettelse af Tæl funktioner. Den første metode anvender Betinget sum og den anden metode anvender 'where-delsætningen. Disse kan derefter sammenføjet med den oprindelige tabel (ved hjælp af kolonner for primære nøgler) for at få Tæl funktioner sammen med de oprindelige data.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Generering af binning funktion

I følgende eksempel viser, hvordan til at generere binned funktioner ved binning (ved hjælp af 5 placeringer) en numerisk kolonne, der kan bruges som en funktion i stedet:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Rullende funktioner fra en enkelt kolonne

I dette afsnit skal viser vi, hvordan du udrulning en enkelt kolonne i en tabel til at generere yderligere funktioner. Det antages, at der er en længde eller breddegrader kolonne i tabellen, hvorfra du forsøger at generere funktioner.

Her er en kort grundlæggende om længde-/ breddegrader placeringsdata (tildelt ressource fra stackoverflow [Sådan mål for nøjagtigheden af bredde- og længdegrader?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Dette er nyttigt at forstå før featurizing placeringsfeltet:

- Logonsiden fortæller os om vi er nord eller Syd, Øst eller Vest på kuglen.
- En undtagen nul hundredvis ciffer fortæller os, bruger vi længdegrader, ikke længde!
- I mange tal giver en placering til om 1.000 kilometer. Det giver os nyttige oplysninger om hvilke kontinent eller Hav vi arbejder på.
- Enheder ciffer (én decimal grad) giver en placering op til 111 kilometer (60 sømil, om 69 sømil). Det kan fortælle os stort set hvad store tilstand eller land, vi er i.
- Første decimal er værd op til 11.1 km: det kan skelne placeringen af en stor by fra en tilstødende store by.
- Anden decimalpunktet er værd op til 1.1 km: det kan adskille én village fra næste.
- Tre decimaler er værd op til 110 m: det kan identificere et stort landbrugsprodukter felt eller institutioner campus.
- I fjerde decimal er værd op til 11 m: det kan identificere en pakke overblik. Det kan sammenlignes med typisk nøjagtigheden af en uændret GPS-enhed med uden forstyrrelser.
- Femte decimalpunktet er værd op til 1.1 m: det skelne træer fra hinanden. Nøjagtigheden til dette niveau med kommercielle GPS-enheder kan kun opnås med ændret rettelse.
- Sjette decimalpunktet er værd op til 0.11 m: Du kan bruge dette til at opbygge strukturer i detaljer, til design af landskaber, opbygge veje. Det skal være mere end godt nok til at spore flytning af halvt og floder. Dette kan opnås ved at tage painstaking mål med GPS, såsom differentially rettede GPS.

Oplysninger om placering af kan være featurized som følger adskillelse område, placering og by oplysninger. Bemærk, at du kan også ringe en RESTEN slutpunkt som Bing Maps API, der er tilgængelige på [Find en placering med punkt](https://msdn.microsoft.com/library/ff701710.aspx) til at få oplysninger, region/distrikt.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Funktionerne ovenfor placering-baserede kan yderligere bruges til at generere yderligere Tæl funktioner som beskrevet tidligere. 


> [AZURE.TIP] Du kan automatisk indsætte poster ved hjælp af dit sprog. Du skal muligvis indsætte data i dele til at forbedre Skriv effektivitet (for et eksempel på, hvordan du gør dette ved hjælp af pyodbc skal du se [A HelloWorld eksempel for at få adgang til SQL Server med python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). En anden mulighed er at indsætte data i databasen ved hjælp af [BCP utility](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Oprette forbindelse til Azure maskine undervisning

Funktionen nyligt oprettede kan tilføjes som en kolonne i en eksisterende tabel eller gemt i en ny tabel og tilsluttet sig med den oprindelige tabel til at lære mere computer. Funktioner kan oprettes eller åbnes, hvis du allerede har oprettet, ved hjælp af [Importdata] [ import-data] modul i Azure Machine Learning som vist nedenfor:

![azureml læsere][1] 

##<a name="python"></a>Ved hjælp af et programmeringssprog som Python

Bruge Python til at udforske data og oprette funktioner, når dataene er i SQL Server minder om behandling af data i Azure blob ved hjælp af Python, som beskrevet i [processen Azure Blob-data i du data videnskabelige miljø](machine-learning-data-science-process-data-blob.md). Dataene skal være indlæst fra databasen i en pandas data ramme og derefter kan behandles yderligere. Vi dokumenterer processen med at oprette forbindelse til databasen og indlæsning af dataene i rammen data i dette afsnit.

Det følgende formatet af forbindelsesstrengen kan bruges til at oprette forbindelse til en SQL Server-database fra Python ved hjælp af pyodbc (Erstat servernavn, dbname, brugernavn og din adgangskode med dine specifikke værdier):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas bibliotek](http://pandas.pydata.org/) i Python indeholder et bredt udvalg af datastrukturer og dataanalyseværktøjer til data strengmanipulation til Python programmering. Nedenstående kode læser resultaterne returneres fra en SQL Server-database til en Pandas data ramme:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Du kan nu arbejde med Pandas data ramme, som er omfattet i artiklen [proces Azure Blob-data i du data videnskabelige miljø](machine-learning-data-science-process-data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Azure Data videnskabelige i handling eksempel

Du kan finde et til slut gennemgang eksempel på brug af et offentligt datasæt Azure Data videnskabelige processen, [Azure Data videnskabelige proces i aktion](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
