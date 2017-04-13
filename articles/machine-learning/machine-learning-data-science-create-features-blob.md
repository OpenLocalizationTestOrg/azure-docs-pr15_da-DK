<properties
    pageTitle="Oprette funktioner til Azure blob storage data ved hjælp af Panda | Microsoft Azure"
    description="Sådan oprettes funktioner for data, der er gemt i pakken Panda Python Azure blob beholder."
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
    ms.author="bradsev;garye" />

#<a name="create-features-for-azure-blob-storage-data-using-panda"></a>Oprette funktioner til Azure blob storage data ved hjælp af Panda

Dette dokument viser, hvordan du opretter funktioner til data, der er gemt i Azure blob beholder ved hjælp af pakken [Pandas](http://pandas.pydata.org/) Python. Efter oprettelse af dispositioner Sådan indlæser data til en Panda data ramme, viser det, hvordan til at generere kategoriske funktioner bruger Python scripts med indikatorværdier og binning funktioner.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]I denne **menu** links til emner, der beskriver, hvordan du opretter funktioner til data i forskellige miljøer. Denne opgave er et trin i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Forudsætninger

Denne artikel forudsætter, at du har oprettet en Azure blob storage konto og har gemt dataene der. Hvis du har brug for instruktioner til at konfigurere en konto, kan du se [oprette en Azure-lager-konto](../storage/storage-create-storage-account.md#create-a-storage-account)


## <a name="load-the-data-into-a-pandas-data-frame"></a>Indlæs data til en Pandas data ramme
For at udforske og manipulere et datasæt, skal den hentes fra blob kilden til en lokal fil, der kan indlæses i en Pandas data ramme. Her er trinnene for at følge denne fremgangsmåde:

1. Hent data fra Azure blob med følgende eksempel Python kode ved hjælp af blob-tjenesten. Erstat variablen i koden under med dine specifikke værdier:

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

##<a name="blob-featuregen"></a>Generering af funktion

De næste to afsnit viser, hvordan til at generere kategoriske funktioner med indikatorværdier og binning funktioner, der bruger Python scripts.

###<a name="blob-countfeature"></a>Indikator for værdi baseret generering af funktion

Kategoriske funktioner kan oprettes på følgende måde:

1. Undersøge fordelingen af kolonnen kategoriske:

        dataframe_blobdata['<categorical_column>'].value_counts()

2. Opret indikatorværdier for hver af kolonneværdierne

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Deltage i statuskolonnen med den oprindelige data ramme

            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Fjerne den oprindelige variabel selve:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

###<a name="blob-binningfeature"></a>Generering af binning funktion

Til at oprette binned funktioner, fortsættes på følgende måde:

1. Tilføje en række kolonner til beholder en numerisk kolonne

        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)

2. Konvertere binning til en sekvens af boolesk variabler

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')

3. Til sidst skal deltage i dummy variablerne tilbage til den oprindelige data ramme

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

##<a name="sql-featuregen"></a>Skrive data tilbage til Azure blob og forbrug i Azure Machine Learning

Når du har gjort dataene og oprettet de nødvendige funktioner, du kan overføre dataene (prøver eller featurized) til en Azure blob og forbruge den Azure maskine lære at benytte følgende fremgangsmåde: Bemærk, at yderligere funktioner kan oprettes i den Azure Machine Learning Studio samt.
1. Skrive data ramme til lokale filer

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Overføre data til Azure blob på følgende måde:

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

3. Nu kan du læse dataene fra blob bruger modulet Azure Machine Learning [Importdata](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , som vist i skærmbilledet nedenfor:

![læser blob](./media/machine-learning-data-science-process-data-blob/reader_blob.png)
