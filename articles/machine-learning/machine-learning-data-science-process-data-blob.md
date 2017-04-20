<properties 
    pageTitle="Behandle Azure blob-data med avanceret analyse | Microsoft Azure" 
    description="Behandle Data i Azure Blob storage." 
    services="machine-learning,storage" 
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

#<a name="heading"></a>Behandle Azure blob-data med avanceret analyse

I dette dokument beskrives undersøgelse data og generere funktioner fra data, der er gemt i Azure Blob storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Indlæse data i en ramme med Pandas data
For at undersøge og manipulere et dataset, skal der hentes fra blob-kilde til en lokal fil, der kan indlæses i en ramme med Pandas data. Her er trinnene til at følge denne fremgangsmåde:

1. Hente data fra Azure blob med følgende Python eksempelkode ved hjælp af blob-service. Erstatte variablen i koden nedenfor med dine specifikke værdier: 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Du kan læse dataene i en Pandas data-ramme fra den hentede fil.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Du er nu klar til at undersøge dataene og oprette funktioner på dette dataset.


##<a name="blob-dataexploration"></a>Undersøgelse af data

Her er nogle eksempler på måder at udforske data ved hjælp af Pandas:

1. Kontrollere antallet af rækker og kolonner 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Undersøg de første eller sidste få rækker i dataset som nedenfor:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Kontrollere feltets datatype hver kolonne blev importeret som at bruge følgende eksempelkode
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Således kontrollere grundlæggende statistikkerne for kolonnerne i datasættet
 
        dataframe_blobdata.describe()
    
5. Se på antallet af poster for hver kolonneværdi som følger

        dataframe_blobdata['<column_name>'].value_counts()

6. Antal manglende værdier sammenlignet med det faktiske antal poster i hver kolonne ved hjælp af følgende eksempelkode

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Hvis der mangler værdier for en bestemt kolonne i dataene, kan du slippe dem på følgende måde:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    Der er en anden måde at erstatte manglende værdier med funktionen tilstand:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Oprette et histogram observationsområde med variabelt antal placeringer til at afbilde fordelingen af en variabel 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Betragter korrelationer mellem variabler ved hjælp af en scatterplot eller ved hjælp af korrelationsfunktionen indbygget

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>Oprettelse af funktion
    
Vi kan generere funktioner ved hjælp af Python på følgende måde:

###<a name="blob-countfeature"></a>Indikatorværdien baseret funktion Generation

Kategoriske funktioner kan oprettes på følgende måde:

1. Undersøg kolonnen kategoriske fordeling:
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. Opret indikatorværdier for hver kolonneværdier

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Deltag i statuskolonnen med den oprindelige data ramme 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Fjerne den oprindelige variabel sig selv:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Binning funktion Generation

Til at generere binned funktioner, fortsættes som følger:

1. Føj en sekvens af kolonner til placering af en numerisk kolonne
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. Konvertere binning til en sekvens af booleske variabler

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. Endelig skal deltage i prøvedukkens variablerne tilbage til den oprindelige ramme data

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>Skrive dataene tilbage på Azure blob og forbruge i Azure Machine Learning

Når du har gjort dataene og oprettet de nødvendige funktioner, kan du overføre data (prøver eller featurized) til en Azure blob og forbruges i Azure Machine Learning ved hjælp af følgende trin: Bemærk, at der kan oprettes flere funktioner i de Azure Machine Learning Studio samt. 
1. Skrive data rammen til lokal fil

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Overføre dataene til blob Azure:

        from azure.storage.blob import BlobService
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

3. Nu kan læse data fra blob ved hjælp af Azure Machine Learning [Importdata] [ import-data] modul, som vist på skærmbilledet nedenfor:
 
![Reader-blob][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
