<properties 
    pageTitle="Få adgang til datasæt med Machine Learning Python klientbibliotek | Microsoft Azure" 
    description="Installere og bruge biblioteket Python klienten til at få adgang til og administrere Azure Machine Learning data sikkert fra et lokalt miljø Python." 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Access datasæt med Python ved hjælp af biblioteket Azure Machine Learning Python klient 

Eksempel på Microsoft Azure Machine Learning Python klientbibliotek kan aktivere sikker adgang til din Azure Machine Learning datasæt fra et lokalt miljø Python og giver mulighed for oprettelse og administration af datasæt i et arbejdsområde.

Dette emne indeholder en vejledning til, hvordan du:

* installere biblioteket Machine Learning Python klient 
* få adgang til og overføre datasæt, herunder oplysninger om, hvordan du får tilladelse til at få adgang til Azure Machine Learning datasæt fra dit lokale Python-miljø
*  få adgang til mellemliggende datasæt fra forsøg
*  Brug biblioteket Python klienten til at Optæl datasæt, få adgang til metadata, læse indholdet af et datasæt, oprette nye datasæt eller opdatere eksisterende datasæt

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Forudsætninger

Biblioteket Python klient er testet under følgende miljøer:

 - Windows, Mac og Linux
 - Python 2.7, 3.3 og 3.4

Det har en afhængighed på følgende pakker:

 - anmodninger om
 - Python dateutil
 - Pandas

Det anbefales at bruge en Python fordeling som [Anaconda](http://continuum.io/downloads#all) eller [kronetaget](https://store.enthought.com/downloads/), som følger med Python, IPython og de tre pakker, der er anført ovenfor installeret. Selvom IPython ikke er absolut påkrævet, er det en god miljø til manipulere og visualisere data interaktivt.


###<a name="installation"></a>Sådan installeres biblioteket Azure Machine Learning Python klient

Biblioteket Azure Machine Learning Python klienten skal også være installeret for at fuldføre de opgaver, der er beskrevet i dette emne. Den er tilgængelig fra [Python pakke indeks](https://pypi.python.org/pypi/azureml). For at installere det i dit miljø, Python skal du køre følgende kommando fra dit lokale Python-miljø:

    pip install azureml

Alternativt kan du hente og installere fra kilder på [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Hvis du har ciffer, der er installeret på computeren, kan du bruge pip for at installere direkte fra ciffer lager:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Brug Studio kodestykker til at få adgang til datasæt

Biblioteket Python klient får du automatisk adgang til din eksisterende datasæt fra forsøg, der er udført.

Fra Studio webbrugergrænsefladen, kan du oprette kodestykker, der omfatter alle de nødvendige oplysninger for at hente og deserialisere datasæt som Pandas DataFrame objekter på din computer og placering.

### <a name="security"></a>Sikkerhed i forbindelse med adgang til data

De kodestykker, der leveres af Studio til brug med Python klient-bibliotek indeholder dit arbejdsområde-id og godkendelse token. Dette giver fuld adgang til dit arbejdsområde og skal beskyttes, som en adgangskode.

Af sikkerhedsmæssige årsager-kode kodestykke funktionalitet er kun tilgængelig for brugere, der har deres rolle, der er angivet som **ejer** til arbejdsområdet. Din rolle vises i Azure Machine Learning Studio på siden **brugere** under **Indstillinger**.

![Sikkerhed][security]

Hvis din rolle ikke er angivet som **ejer**, kan du enten anmodning inviteres igen som en ejer, eller Kontakt ejeren af arbejdsområdet til at give dig kodestykket.

Hvis du vil have tilladelsen tokenet, kan du gøre et af følgende:



- Bede om en token en ejer. Ejere kan få adgang til deres godkendelse tokens fra siden Indstillinger for deres arbejdsområde i Studio. Vælg **Indstillinger** i venstre rude, og klikke på **Godkendelse TOKENS** for at få vist de primære og sekundære tokens.  Selvom primært eller sekundære godkendelse tokens kan bruges i kodestykket, anbefales det, at ejere kun dele de sekundære godkendelse tokens.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Bede om at blive forfremmet til rolle ejer.  For at gøre dette, skal en nuværende ejer af arbejdsområdet først fjerne du fra arbejdsområdet og derefter igen invitere dig til den som en ejer.

Når udviklere har anskaffet arbejdsområde-id og godkendelse adgangstoken, de har adgang til arbejdsområdet ved hjælp af kodestykke uanset deres rolle.

Godkendelse tokens administreres på siden **Godkendelse TOKENS** under **Indstillinger**. Du kan gendanne dem, men denne procedure tilbagekalder adgang til den foregående token.

### <a name="accessingDatasets"></a>Access datasæt fra et lokalt Python program

1. Klik på **DATASÆT** på navigationslinjen til venstre i Machine Learning Studio.

2. Vælg de datasæt, du vil have adgang til. Du kan vælge nogen af DataSet, fra listen **Mine DATASÆT** eller fra listen **eksempler** .

3. Klik på **Generer Data Access kode**fra værktøjslinjen nederst. Hvis dataene er i et format, der er ikke kompatibelt med biblioteket Python klient, deaktiveres denne knap.

    ![Datasæt][datasets]

4. Vælg kodestykket det vindue, der vises og kopiere det til Udklipsholder.

    ![Pinkode][dataset-access-code]

5. Indsæt koden i notesbog til dit lokale Python program.

    ![Notesbog][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Access mellemliggende datasæt fra Machine Learning forsøg

Når et forsøg er kørt i Machine Learning Studio, er det muligt at få adgang til den mellemliggende datasæt fra noderne output af moduler. Mellemliggende datasæt er data, der er oprettet og bruges til mellemliggende trin, når en model-værktøjet er kørt.

Mellemliggende datasæt kan åbnes, så længe dataformatet er kompatibelt med biblioteket Python klient.

Følgende filformater understøttes (konstanter til disse er i den `azureml.DataTypeIds` klasse):

 - Almindelig tekst
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Du kan bestemme formatet ved at holde musen over en modul output node. Det vises sammen med navnet på knude i et værktøjstip.

Nogle af modulerne, som [delt] [ split] modul, output til et format med navnet `Dataset`, som ikke understøttes af biblioteket Python klient.

![Datasæt Format][dataset-format]

Du skal bruge et konvertering modul, som [konvertere til CSV-][convert-to-csv], får du en output ind i et format, der understøttes.

![GenericCSV Format][csv-format]

I nedenstående fremgangsmåde vises et eksempel, der opretter et forsøg, kører den og får adgang til mellemliggende datasættet.

1. Oprette en ny forsøg.

2. Indsæt en **voksne tælling indtægter binære klassifikation dataset** modul.

3. Indsætte en [delt] [ split] modulet og oprette forbindelse til dens input til datasæt modul output.

4. Indsætte en [konvertere til CSV-] [ convert-to-csv] modulet og oprette forbindelse til dens input til en [delt] [ split] modul skriver.

5. Gem forsøget, kører den, og vente på afslutning.

6. Klik på noden output på [Konverter til CSV-] [ convert-to-csv] modul.

7. Når genvejsmenuen vises, skal du vælge **generere Data Access kode**.

    ![Genvejsmenu][experiment]

8. Vælg kodestykket og kopiere den til din Udklipsholder fra det vindue, der vises.

    ![Pinkode][intermediate-dataset-access-code]

9. Indsæt koden i din notesbog.

    ![Notesbog][ipython-intermediate-dataset]

10. Du kan få vist dataene ved hjælp af matplotlib. Dette viser i et histogram for kolonnen alder:

    ![Histogram][ipython-histogram]


##<a name="clientApis"></a>Brug biblioteket Machine Learning Python klienten til at få adgang til, læse, oprette og administrere datasæt

### <a name="workspace"></a>Arbejdsområde

Arbejdsområdet er indgangspunkt for biblioteket Python klient. Angiv den `Workspace` token klassen med dit arbejdsområde-id og tilladelse til at oprette en forekomst:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Optæl datasæt

Optælle alle datasæt i en given arbejdsområde:

    for ds in ws.datasets:
        print(ds.name)

Optælles lige brugeroprettede-datasæt:

    for ds in ws.user_datasets:
        print(ds.name)

Optælles lige eksempel datasæt:

    for ds in ws.example_datasets:
        print(ds.name)

Du kan få adgang til et datasæt ved navn (som er store og små bogstaver):

    ds = ws.datasets['my dataset name']

Eller du kan få adgang til det efter indeks:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata

Datasæt har metadata, ud over indhold. (Mellemliggende datasæt er en undtagelse til denne regel og har ikke en hvilken som helst metadata).

Nogle metadataværdier, som er tildelt af brugeren på klokkeslættet for oprettelsen af:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Andre er værdier, der er tildelt af Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Se den `SourceDataset` klasse mere at vide om de tilgængelige metadata.


### <a name="read-contents"></a>Læse indholdet

De kodestykker, der leveres af Machine Learning Studio automatisk hente og deserialisere datasæt til et Pandas DataFrame objekt. Dette er færdig med den `to_dataframe` metode:

    frame = ds.to_dataframe()

Hvis du foretrækker at hente den rækkedata og udføre deserialiseringen dig selv, er det en indstilling. I øjeblikket er dette den eneste mulighed for formater som 'ARFF', som ikke kan annullere serialiseringen biblioteket Python klient.

At læse oplysningerne som tekst:

    text_data = ds.read_as_text()

At læse oplysningerne som binært tal:

    binary_data = ds.read_as_binary()

Du kan også bare åbne en stream til indholdet:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Oprette et nyt datasæt

Biblioteket Python klienten gør det muligt at overføre datasæt fra Python programmet. Disse datasæt er derefter tilgængelige til brug i arbejdsområdet.

Hvis du har dine data i en Pandas DataFrame, kan du bruge følgende kode:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Hvis dine data er allerede serienummereret, kan du bruge:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Biblioteket Python klient er kunne sekventielt en Pandas DataFrame i følgende filformater (konstanter til disse er i den `azureml.DataTypeIds` klasse):

 - Almindelig tekst
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Opdatere et eksisterende datasæt

Hvis du forsøger at overføre et nyt datasæt med et navn, der svarer til en eksisterende datasæt, skal du få fejlen konflikt.

Hvis du vil opdatere et eksisterende datasæt, skal du først få en reference til den eksisterende datasæt:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Brug derefter `update_from_dataframe` sekventielt og erstatte indholdet af datasættet på Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Hvis du vil sekventielt dataene til et andet format, skal du angive en værdi for det valgfri `data_type_id` parameter.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Du kan eventuelt angive en ny beskrivelse ved at angive en værdi for den `description` parameter.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Du kan eventuelt angive et nyt navn ved at angive en værdi for den `name` parameter. Fra nu af, skal du hente datasættet ved hjælp af det nye navn. Følgende kode opdaterer data, navn og beskrivelse.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Den `data_type_id`, `name` og `description` parametre er valgfri og standardfaner til deres forrige værdi. Den `dataframe` parameter er altid påkrævet.

Hvis dine data er allerede serienummereret, kan du bruge `update_from_raw_data` i stedet for `update_from_dataframe`. Hvis du kun sender i `raw_data` i stedet for `dataframe`, det fungerer på samme måde.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
