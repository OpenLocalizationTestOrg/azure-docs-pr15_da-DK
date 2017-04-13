<properties 
    pageTitle="Udforske data i SQL Server Virtual Machine på Azure | Microsoft Azure" 
    description="Sådan udforske data, der er gemt i en SQL Server VM på Azure." 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Udforske data i SQL Server Virtual Machine på Azure


Dette dokument omhandler, hvordan at udforske data, der er gemt i en SQL Server VM på Azure. Dette kan gøres ved data wrangling ved hjælp af SQL eller ved hjælp af et programmeringssprog som Python.

Følgende **menuen** links til emner, der beskriver, hvordan du bruger værktøjer til at udforske data fra forskellige lagerplads miljøer. Denne opgave er et trin i Cortana Analytics proces (KNOP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] Eksempel på SQL-sætningerne i dette dokument forudsætter, at dataene er i SQL Server. Hvis det ikke referere til skyen data videnskabelige procesoversigten lære at flytte dine data til SQL Server.



## <a name="sql-dataexploration"></a>Udforske SQL data med SQL-scripts

Her er nogle eksempler på SQL-scripts, som kan bruges til at udforske data butikker i SQL Server.

1. Få antallet af observationer dagen

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Få niveauer i en kategoriske kolonne

    `select  distinct <column_name> from <databasename>`

3. Få antallet niveauer i kombination af to kategoriske kolonner 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Få en fordeling for numeriske kolonner

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Du kan bruge [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) og referere til IPNB med titlen [NYC Data wrangling med IPython notesbog og SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) for en til slut gennemgå praktiske f.eks.

##<a name="python"></a>Udforske SQL data med Python

Bruge Python til at udforske data og oprette funktioner, når dataene er i SQL Server minder om behandling af data i Azure blob ved hjælp af Python, som beskrevet i [processen Azure Blob-data i dit miljø, videnskabelige data](machine-learning-data-science-process-data-blob.md). Dataene skal være indlæst fra databasen i en pandas DataFrame og derefter kan behandles yderligere. Vi dokumenterer processen med at oprette forbindelse til databasen og indlæsning af dataene i DataFrame i dette afsnit.

Det følgende formatet af forbindelsesstrengen kan bruges til at oprette forbindelse til en SQL Server-database fra Python ved hjælp af pyodbc (Erstat servernavn, dbname, brugernavn og adgangskode til din specifikke værdier):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas bibliotek](http://pandas.pydata.org/) i Python indeholder et bredt udvalg af datastrukturer og dataanalyseværktøjer til data strengmanipulation til Python programmering. Følgende kode læser resultaterne returneres fra en SQL Server-database til en Pandas data ramme:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Du kan nu arbejde med Pandas DataFrame, som beskrevet i emnet [proces Azure Blob-data i dit miljø, videnskabelige data](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Cortana Analytics proces i handling eksempel

Se et eksempel på slutpunkt til gennemgang af Cortana Analytics processen ved hjælp af et offentligt datasæt, under [Team Data videnskabelige proces i praksis: Brug af SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
