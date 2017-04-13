<properties 
    pageTitle="Udforske data i Azure blob-lager med Pandas | Microsoft Azure" 
    description="Sådan udforske data, der er gemt i Azure blob beholder ved hjælp af Pandas." 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>Udforske data i Azure blob-lager med Pandas

Dette dokument omhandler, hvordan at udforske data, der er gemt i Azure blob beholder ved hjælp af [Pandas](http://pandas.pydata.org/) Python pakke.

Følgende **menuen** links til emner, der beskriver, hvordan du bruger værktøjer til at udforske data fra forskellige lagerplads miljøer. Denne opgave er et trin i [Data videnskabelige proces]().

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>Forudsætninger
I denne artikel antager, at du har:

* Oprettet en Azure-lager-konto. Hvis du har brug for instruktioner, se [oprette en Azure-lager-konto](../storage/storage-create-storage-account.md#create-a-storage-account)
* Gemt dine data i en Azure blob storage kontoen. Hvis du har brug for instruktioner, kan du se [skifte data til og fra Azure-lager](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Indlæs data til en Pandas DataFrame
For at udforske og manipulere et datasæt, skal den først hentes fra blob kilden til en lokal fil, der kan indlæses i en Pandas DataFrame. Her er trinnene for at følge denne fremgangsmåde:

1. Hent data fra Azure blob med følgende Python eksempel-kode ved hjælp af blob-tjenesten. Erstat variablen i følgende kode med dine specifikke værdier: 

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


2. Læs dataene til en Pandas data-ramme fra den hentede fil.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nu er du klar til at udforske data og oprette funktioner på dette dataset.

##<a name="blob-dataexploration"></a>Eksempler på undersøgelse af data ved hjælp af Pandas

Her er nogle få eksempler på forskellige måder at udforske data ved hjælp af Pandas:

1. Undersøge **antallet af rækker og kolonner** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Undersøg** de første eller sidste par **rækker** i følgende datasættet:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Marker den **datatype** hver kolonne, som er importeret som via det følgende eksempel
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Angiv **grundlæggende statistik** for kolonnerne i datasættet på følgende måde
 
        dataframe_blobdata.describe()
    
5. Se på antallet af poster for hver kolonneværdi som følger

        dataframe_blobdata['<column_name>'].value_counts()

6. **Tælle manglende værdier** kontra det faktiske antal poster i hver kolonne via det følgende eksempel

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Hvis du har **manglende værdier** for en bestemt kolonne i dataene, kan du slippe dem på følgende måde:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    En anden måde at erstatte manglende værdier er med funktionen tilstand:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Oprette et **histogram** afbildning ved hjælp af variable antallet af placeringer afbilde fordelingen af en variabel 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Se på **korrelationer** mellem variabler ved hjælp af en scatterplot eller bruge korrelationsfunktionen indbyggede

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
