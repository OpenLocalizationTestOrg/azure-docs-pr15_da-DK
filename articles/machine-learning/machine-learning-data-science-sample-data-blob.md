<properties 
    pageTitle="Eksempeldata i Azure blob storage | Microsoft Azure" 
    description="Eksempeldata i Azure Blob-lager" 
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

#<a name="heading"></a>Eksempeldata i Azure blob storage


Dette dokument omhandler stikprøver, hvor data, der er gemt i Azure blob-lager ved at hente det fra et program og indsamle den ved hjælp af procedurer, der er skrevet i Python.

**Hvorfor Lyt til dine data?**
Hvis du planlægger at analysere datasættet er stort, er det som regel er en god ide at ned stikprøver data for at reducere den til en mindre, men repræsentant og mere håndterbare størrelse. Dette gør det nemmere at forstå data, udforske og funktion teknisk. Dens rolle i Cortana Analytics processen er at aktivere hurtig prototyper af databehandlingsfunktioner og machine learning-modeller.

I **menuen** under links til emner, der beskriver, hvordan Indsaml data fra forskellige lagerplads miljøer. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Denne stikprøver, hvor opgaven er et trin i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="download-and-down-sample-data"></a>Download og ned-eksempeldata
1. Hente data fra Azure blob-lager ved hjælp af tjenesten blob fra følgende eksempel Python kode: 

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

2. Du kan læse data til en Pandas data-ramme fra den fil, der er hentet ovenfor.

        import pandas as pd

        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Ned-stikprøve data ved hjælp af den `numpy`'s `random.choice` på følgende måde:

        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Nu kan du arbejde med ovenstående data rammen med 1% stikprøvernes til mere dybtgående og generering af funktion.

##<a name="heading"></a>Overføre data og læse den til Azure Machine Learning

Du kan bruge følgende eksempelkode til ned stikprøver dataene og bruge det direkte i Azure ML:

1. Skrive data ramme til en lokal fil

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Overfør den lokale fil på en Azure blob via det følgende eksempel:

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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Læse dataene fra Azure blob ved hjælp af Azure ML [Importdata](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , som vist i billedet nedenfor:
 
![læser blob](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

 
