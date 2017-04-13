<properties 
    pageTitle="Eksempel på Data i SQL Server på Azure | Microsoft Azure" 
    description="Eksempeldata i SQL Server på Azure" 
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
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Eksempeldata i SQL Server på Azure


Dette dokument viser, hvordan du eksempeldata, der er gemt i SQL Server på Azure ved hjælp af SQL eller programmeringssproget Python. Sådan flytter du indsamlede data til Azure Machine Learning ved at gemme det på en fil, overføre den til en Azure blob og læser den i Azure Machine Learning Studio vises også.

Python udvalg bruger [pyodbc](https://code.google.com/p/pyodbc/) ODBC-biblioteket til at oprette forbindelse til SQL Server på Azure og biblioteket [Pandas](http://pandas.pydata.org/) for at gøre udvalg.

>[AZURE.NOTE] Eksempel på SQL-koden i dette dokument antages det, at dataene er i en SQL Server på Azure. Hvis det ikke er, kan du se [flytte data til SQL Server på Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) emnet for vejledning i at flytte dine data til SQL Server på Azure.

**Hvorfor Lyt til dine data?**
Hvis du planlægger at analysere datasættet er stort, er det som regel er en god ide at ned stikprøver data for at reducere den til en mindre, men repræsentant og mere håndterbare størrelse. Dette gør det nemmere at forstå data, udforske og funktion teknisk. Dens rolle i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) er at aktivere hurtig prototyper af databehandlingsfunktioner og machine learning-modeller.

I **menuen** under links til emner, der beskriver, hvordan Indsaml data fra forskellige lagerplads miljøer. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Denne stikprøver, hvor opgaven er et trin i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Ved hjælp af SQL

I dette afsnit beskrives forskellige metoder ved hjælp af SQL til at udføre enkle stikprøver mod dataene i databasen. Vælg en metode, der er baseret på datastørrelsen på dine og dens distributionsgrupper.

To elementerne nedenfor viser, hvordan du bruger newid i SQL Server til at udføre stikprøverne. Den metode, du vælger, afhænger af, hvordan tilfældige du vil prøve skal være (pk_id i nedenstående eksempelkode antages for at være en primær nøgle af automatisk oprettede).

1. Mindre strenge stikprøve

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Mere stikprøve 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample kan være bruges til udvalg, såvel som vist nedenfor. Det kan være en bedre fremgangsmåde, hvis dine data er stor (forudsætter, at der ikke er mellem data på forskellige sider) og til forespørgslen for at fuldføre en begrundet tidspunkt.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Du kan udforske og generere funktioner fra denne indsamlede data ved at gemme den i en ny tabel


###<a name="sql-aml"></a>Oprette forbindelse til Azure maskine undervisning

Du kan bruge eksempel forespørgsler over direkte i Azure Machine Learning [Importdata] [ import-data] modul ned stikprøver dataene på farten og sætter det i et Azure Machine Learning forsøg. Et skærmbillede af brugen af reader-modulet til at læse de indsamlede data er vist nedenfor:
   
![læser sql][1]

##<a name="python"></a>Brug af Python programmeringssproget 

Dette afsnit demonstrerer brugen af [pyodbc bibliotek](https://code.google.com/p/pyodbc/) for at oprette en ODBC oprette forbindelse til en SQL server-database i Python. Forbindelsesstrengen til databasen er som følger: (erstatte servernavn, dbname, brugernavn og din adgangskode med konfigurationen af):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Biblioteket [Pandas](http://pandas.pydata.org/) i Python indeholder et bredt udvalg af datastrukturer og dataanalyseværktøjer til data strengmanipulation til Python programmering. Nedenstående kode læser en 0,1% eksempel på dataene fra en tabel i Azure SQL-database i en Pandas data:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Du kan nu arbejde med indsamlede data i Pandas data ramme. 

###<a name="python-aml"></a>Oprette forbindelse til Azure maskine undervisning

Du kan bruge følgende eksempelkode til at gemme dataene prøver fra ned til en fil, og Overfør det til en Azure blob. Dataene i blob kan direkte læses i Azure Machine Learning forsøg ved hjælp af [Importdata] [ import-data] modul. Trinnene er som følger: 

1. Skrive pandas data ramme til en lokal fil

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Overføre lokal fil til Azure blob

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Læse data fra Azure blob ved hjælp af Azure Machine Learning [Importdata] [ import-data] modul som vist i på skærmen greb nedenfor:
 
![læser blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Team Data videnskabelige processen i handling eksempel

Eksempelvis til slut gennemgang af processen Team Data videnskabelige bruger et offentligt datasæt, se [Team Data videnskabelige proces i praksis: Brug af SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
