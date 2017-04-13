<properties
    pageTitle="Henblik på omstilling Machine Learning modeller fra et program | Microsoft Azure"
    description="Lær, hvordan du automatisk henblik på omstilling en model og opdatere webtjeneste for at bruge den nyligt erfaren model i Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Henblik på omstilling Machine Learning modeller fra et program  

I denne gennemgang skal lære du, hvordan du automatisk henblik på omstilling en Azure Machine Learning-webtjeneste bruger C# og Machine Learning batchen udførelse af tjenesten.

Når du har retrained modellen, viser, hvordan du opdaterer modellen i din skønnet webtjeneste følgende gennemgange:

- Hvis du har installeret en klassisk webtjeneste i portalen Machine Learning webtjenester, kan du se [henblik på omstilling en klassisk webtjeneste](machine-learning-retrain-a-classic-web-service.md). 
- Hvis du har installeret en ny webtjeneste, kan du se [henblik på omstilling en ny webtjeneste ved hjælp af den maskine Learning cmdletter til administration af](machine-learning-retrain-new-web-service-using-powershell.md).

Se [henblik på omstilling en Machine Learning Model](machine-learning-retrain-machine-learning-model.md)for en oversigt over processen retraining.

Hvis du vil starte med dine eksisterende nye Azure-ressourcestyring baseret webtjeneste, skal du se [henblik på omstilling en eksisterende skønnet webtjeneste](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Oprette et kursus forsøg
 
I dette eksempel skal du bruge "eksempel 5: tog, Test, Evaluer til binær klassifikation: voksne datasæt" fra Microsoft Azure Machine Learning eksemplerne. 
    
Sådan oprettes forsøget:

1.  Log på til Microsoft Azure Machine Learning Studio. 
2.  Den nederste højre hjørne af dashboardet, klik på **Ny**.
3.  Vælg eksempel 5 Samples Microsoft.
4.  Hvis du vil omdøbe forsøget øverst på lærredet forsøg Vælg forsøg navnet "eksempel 5: tog, Test, Evaluer til binær klassifikation: voksne Dataset".
5.  Skriv tælling Model.
6.  I bunden af forsøget lærred, klik på **Kør**.
7.  Klik på **Konfigurer webtjeneste** , og vælg **Retraining webtjeneste**. 

    ![Indledende forsøg.][2]

Diagram 2: Indledende forsøg.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Oprette en skønnet forsøg og udgive som en webtjeneste  

Derefter kan du oprette en Predicative forsøg.

1.  Klik på **Angiv webtjeneste** i bunden af forsøg lærredet, og vælg **Skønnet webtjeneste**. Dette gemmer modellen som en erfaren Model og tilføjer web service Input og Output moduler. 
2.  Klik på **Kør**. 
3.  Når forsøget er afsluttet, kan du klikke på **Installere webtjeneste [klassisk]** eller **Installere webtjeneste [ny]**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Installere kursus forsøget som en webtjeneste til uddannelse

Hvis du vil henblik på omstilling erfaren modellen, skal du installere forsøget kursus, du har oprettet som en Retraining webtjeneste. Denne webtjeneste skal have et *Web Service Output* modul, der er knyttet til den * [Tog Model] [ train-model] * modul, skal kunne frembringe nye erfaren modeller.

1. For at vende tilbage til kursus forsøget, klik på ikonet forsøg i venstre rude, og klik derefter på forsøget med navnet tælling Model.  
2. Skriv webtjeneste i søgefeltet Søg forsøg elementer. 
3. Træk et *Web Service Input* -modul til forsøg lærredet og forbinde dens output til modulet *Ren manglende Data* .  Dette sikrer, at dataene retraining behandles på samme måde som de oprindelige data i kursus.
4. Træk to *webtjeneste Output* moduler til forsøg lærredet. Oprette forbindelse output fra modulet *Tog Model* til én og output fra modulet *Evaluere Model* til en anden. Web service output til **Tog Model** giver os den nye erfarne model. Output tilknyttet **Evaluere Model** returnerer pågældende modul output, som er performance-resultaterne.
5. Klik på **Kør**. 

Derefter skal du installere kursus forsøget som en webtjeneste, der giver en erfaren model og model evalueringsresultater. For at opnå dette, skal din næste sæt handlinger er afhængige af om du arbejder med en klassisk webtjeneste eller en ny webtjeneste.  
  
**Klassisk webtjeneste**

Klik på **Angiv webtjeneste** i bunden af forsøg lærredet, og vælg **Installere webtjeneste [klassisk]**. **Dashboard** til webtjeneste vises med API-nøgle og siden API hjælp til udførelse af batchen. Kun metoden batchen udførelse af kan bruges til oprettelse af erfaren modeller.

**Ny webtjeneste**

Klik på **Angiv webtjeneste** i bunden af forsøg lærredet, og vælg **Installere webtjeneste [ny]**. Portalen Web Service Azure Machine Learning webtjenester åbnes med Implementer service websiden. Skriv et navn til din webtjeneste og vælge en betalingsplan for, og klik derefter på **Implementer**. Kun metoden batchen udførelse af kan bruges til oprettelse af erfaren modeller

I begge tilfælde, når forsøg er fuldført kører, skal den resulterende arbejdsproces skal se ud som følger:

![Resulterende arbejdsproces efter kørt.][4]

Diagram 3: Resultatet arbejdsproces efter kørt.

## <a name="retrain-the-model-with-new-data-using-bes"></a>Henblik på omstilling modellen med nye data ved hjælp af BES

I dette eksempel skal bruger du C# til at oprette retraining programmet. Du kan også bruge Python eller R eksempelkoden til at udføre denne opgave.

Ringe til de omskoling API'er:

1. Opret et C# Console-program i Visual Studio (ny -> Project -> Windows Desktop -> Console program).
2.  Log på portalen Machine Learning webtjeneste.
3.  Hvis du arbejder med en klassisk webtjeneste, skal du klikke på **Klassisk webtjenester**.
    1.  Klik på den webtjeneste, du arbejder med.
    2.  Klik på standardslutpunkt.
    3.  Klik på **forbruge**.
    4.  Klik på **Batch**nederst på siden **forbrug** i afsnittet **Eksempelkode** .
    5.  Fortsæt til trin 5 i denne procedure.
4.  Hvis du arbejder med en ny webtjeneste, skal du klikke på **Web-tjenester**.
    1.  Klik på den webtjeneste, du arbejder med.
    2.  Klik på **forbruge**.
    3.  Klik på **Batch**nederst på siden forbrug i afsnittet **Eksempelkode** .
5.  Kopiér eksempel C#-koden til udførelse af batchen og sætte det ind i filen Program.cs at sikre, at navneområdet forbliver intakt.

Føje Nuget pakken Microsoft.AspNet.WebApi.Client som angivet i kommentarerne. For at tilføje referencen til Microsoft.WindowsAzure.Storage.dll skal du muligvis først installere biblioteket klienten til Microsoft Azure-lager services. Du kan finde yderligere oplysninger finder [Windows lagerplads Services](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Opdatere apikey erklæring

Find **apikey** definition.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Find den primære nøgle i sektionen **grundlæggende forbrug oplysninger** på siden **forbrug** og kopiere den til **apikey** definition.

### <a name="update-the-azure-storage-information"></a>Opdatere oplysningerne Azure-lager

BES eksempelkoden overfører en fil fra et lokalt drev (For eksempel "C:\temp\CensusIpnput.csv") til Azure-lager, behandler den og skriver resultaterne tilbage til Azure-lager.  

For at udføre denne opgave, skal du hente lagerplads navn, nøgle og objektbeholder kontooplysninger for kontoen lagerplads fra portalen klassisk Azure og Opdater tilsvarende værdier i koden. 

1. Log på portalen klassisk Azure.
1. Klik på **lager**i kolonnen venstre navigationsrude.
1. Vælg én for at gemme retrained modellen fra listen over lagerplads konti.
1. Nederst på siden skal du klikke på **Administrer Access-taster**.
1. Kopiere og gemme den **Primærnøgle i Access** , og Luk dialogboksen. 
1. Klik på **beholdere**øverst på siden.
1. Vælg en eksisterende objektbeholder eller oprette en ny, og Gem navnet.

Find *StorageAccountName*, *StorageAccountKey*og *StorageContainerName* erklæringerne og opdatere de værdier, du har gemt fra Azure-portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Du skal også sørge for input filen er tilgængelig på den placering, du angiver i koden. 

### <a name="specify-the-output-location"></a>Angive en outputplacering

Når du angiver en outputplacering i anmode om overførslen, skal filtypenavnet på den fil, der er angivet i *RelativeLocation* angives som ilearner. 

Se eksemplet nedenfor:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Navnene på dine outputplacering kan være forskellige fra dem i denne gennemgang, der er baseret på den rækkefølge, hvori du har tilføjet web service output moduler. Da du har konfigureret dette kursus forsøg med to output skal medtage resultaterne lagerplads placering oplysninger for dem begge.  

![Omskoling output][6]

Diagram 4: Omskoling output.

## <a name="evaluate-the-retraining-results"></a>Evaluere Retraining resultaterne
 
Når du kører programmet, indeholder output den URL-adresse og SAS token, der er nødvendige for at få adgang til evalueringsresultaterne.

Du kan se resultaterne af retrained modellen ved at kombinere de *BaseLocation*, *RelativeLocation*og *SasBlobToken* fra outputresultaterne af *output2* (som vist i det foregående retraining output billede) og indsætte den fulde URL-adresse i browserens adresselinje.  

Undersøge resultater for at finde ud af, om den nyligt erfaren modellen fungerer godt nok til at erstatte den eksisterende.

Kopiere *BaseLocation*, *RelativeLocation*og *SasBlobToken* fra outputresultater, du vil bruge dem under processen retraining.

## <a name="next-steps"></a>Næste trin

Hvis du har installeret skønnet webtjenesten ved at klikke på **Installere webtjeneste [klassisk]**, kan du se [henblik på omstilling en klassisk webtjeneste](machine-learning-retrain-a-classic-web-service.md).

Hvis du har installeret skønnet webtjenesten ved at klikke på **Installere [ny]-webtjeneste**, kan du se [henblik på omstilling en ny webtjeneste ved hjælp af den maskine Learning cmdletter til administration af](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/