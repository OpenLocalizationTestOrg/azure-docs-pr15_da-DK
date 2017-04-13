<properties
    pageTitle="Henblik på omstilling en eksisterende skønnet webtjeneste | Microsoft Azure"
    description="Lær at henblik på omstilling en model og opdatere webtjeneste for at bruge den nyligt erfaren model i Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Henblik på omstilling en eksisterende skønnet webtjeneste

I dette dokument beskrives retraining processen til følgende scenario:

* Du har et kursus forsøg og en skønnet forsøg, som du har installeret som en opgivet webtjeneste.
* Du har nye data, du vil din skønnet webtjeneste skal bruge til at udføre dens vundne.

Starter med dine eksisterende webtjeneste og forsøg, skal du følge disse trin:

1. Opdater modellen.
    1. Ændre din kursus forsøg at tillade web service input og output.
    2. Installer kursus forsøget som en retraining webtjeneste.
    3. Brug forsøget kursus batchen udførelse af Service (BES) til at henblik på omstilling modellen.
2. Bruge Azure Machine Learning PowerShell-cmdlet'er til at opdatere skønnet forsøget.
    1.  Log på kontoen Azure ressourcestyring.
    2.  Få definitionen af web-tjenesten.
    3.  Eksportere definitionen af web-tjenesten som JSON.
    4.  Opdater referencen til ilearner blob i JSON.
    5.  Importere JSON i en web service definition.
    6.  Opdatere webtjenesten med en ny web service definition.

## <a name="deploy-the-training-experiment"></a>Installere kursus forsøget

Hvis du vil installere kursus forsøget som en retraining webtjeneste, skal du tilføje web service input og output til modellen. Ved at oprette forbindelse et *Web Service Output* -modul til at forsøget * [Tog Model] [ train-model] * modul, du aktiverer kursus forsøget til at oprette en ny erfaren model, som du kan bruge i dine skønnet forsøg. Hvis du har en *Evaluere Model* modulet, kan du også vedhæfte web service output for at få evalueringsresultater som output.

Sådan opdaterer du dine kursus forsøg:

1. Knytte et *Web Service Input* modul til dine data i et felt (for eksempel en *Ren manglende Data* modul). Typisk, du vil sikre dig, at din inputdataene behandles på samme måde som de oprindelige data i kursus.
2. Knytte et *Web Service Output* -modul til output fra din *Tog Model* modul.
3. Hvis du har et *Evaluere Model* modul, og du vil gemme evalueringsresultaterne, kan du oprette forbindelse et *Web Service Output* -modul til output fra din *Evaluere Model* modul.

Køre din forsøg.

Derefter skal du installere kursus forsøget som en webtjeneste, der giver en erfaren model og model evalueringsresultater.  

Klik på **Angiv webtjeneste**nederst i forsøg lærredet, og vælg derefter **Installere webtjeneste [ny]**. Portalen Azure Machine Learning Web Services åbnes med siden **Installere webtjeneste** . Skriv et navn til din webtjeneste, vælge en betalingsplan for, og klik derefter på **Installer**. Du kan kun bruge metoden udførelse af batchen til oprettelse af erfaren modeller.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Henblik på omstilling modellen med nye data ved hjælp af BES

I dette eksempel skal bruger vi C# til at oprette retraining programmet. Du kan også bruge Python eller R eksempel-kode til at udføre denne opgave.

Ringe til de retraining API'er:

1. Opret et C# console-program i Visual Studio (**Ny** > **Project** > **Windows-skrivebord** > **Console program**).
2.  Log på portalen Machine Learning webtjenester.
3.  Klik på den webtjeneste, du arbejder med.
2.  Klik på **forbruge**.
3.  Klik på **Batch**nederst på siden **forbrug** i afsnittet **Eksempelkode** .
5.  Kopiér eksempel C#-koden til udførelse af batchen og sætte det ind i filen Program.cs. Sørg for, at navneområdet forbliver intakt.

Føje NuGet pakken Microsoft.AspNet.WebApi.Client, som angivet i kommentarerne. For at tilføje referencen til Microsoft.WindowsAzure.Storage.dll skal du muligvis først installere [klientbibliotek til Azure-lager tjenester](https://www.nuget.org/packages/WindowsAzure.Storage).

Følgende skærmbillede viser siden **forbrug** i portalen Azure Machine Learning Web Services.

![Bruge side][1]

### <a name="update-the-apikey-declaration"></a>Opdatere apikey erklæring

Find angivelsen **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Find den primære nøgle i sektionen **grundlæggende forbrug oplysninger** på siden **forbrug** og kopiere den til **apikey** definition.

### <a name="update-the-azure-storage-information"></a>Opdatere oplysningerne Azure-lager

BES eksempelkoden overfører en fil fra et lokalt drev (eksempelvis "C:\temp\CensusIpnput.csv") til Azure-lager, behandler den og skriver resultaterne tilbage til Azure-lager.  

Hvis du vil opdatere oplysningerne om Azure-lager, skal du hente kontonavn lager, nøgle og objektbeholder oplysninger til kontoen lagerplads fra Azure klassisk portalen, og derefter opdatere correspondi når du har kørt din forsøg, resulterende arbejdsprocessen skal ligne følgende:

![Resulterende arbejdsproces efter kørt][4]Viser værdierne i koden.

1. Log på portalen Azure klassisk.
1. Klik på **lager**i kolonnen venstre navigationsrude.
1. Vælg én for at gemme retrained modellen fra listen over lagerplads konti.
1. Nederst på siden skal du klikke på **Administrer Access-taster**.
1. Kopiere og gemme den **Primærnøgle i Access** , og Luk dialogboksen.
1. Klik på **beholdere**øverst på siden.
1. Vælg en eksisterende beholder, eller Opret en ny, og Gem navnet.

Find den *StorageAccountName*, *StorageAccountKey*og *StorageContainerName* erklæringer, og opdatere de værdier, du har gemt fra portalen klassisk.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Du skal også sikre, at filen input er tilgængelige på den placering, du angiver kode.

### <a name="specify-the-output-location"></a>Angive en outputplacering

Når du angiver en outputplacering i anmode om overførslen, filtypenavnet på den fil, der er angivet i *RelativeLocation* skal være angivet som `ilearner`. Se eksemplet nedenfor:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Følgende er et eksempel på omskoling output: ![omskoling output][6]

## <a name="evaluate-the-retraining-results"></a>Evaluere retraining resultaterne

Når du kører programmet, omfatter output URL-adresse og delte signaturer adgangstoken, der er nødvendige for at få adgang til evalueringsresultaterne.

Du kan se resultaterne af retrained modellen ved at kombinere *BaseLocation*, *RelativeLocation*og *SasBlobToken* fra outputresultaterne af *output2* , (som vist i det foregående retraining output billede) og indsætte den fulde URL-adresse i browserens adresselinje.  

Undersøge resultater for at finde ud af, om den nyligt erfaren modellen fungerer godt nok til at erstatte den eksisterende.

Kopiere *BaseLocation*, *RelativeLocation*og *SasBlobToken* fra outputresultaterne.

## <a name="retrain-the-web-service"></a>Henblik på omstilling webtjenesten

Når du henblik på omstilling en ny webtjeneste, kan du opdatere definitionen af skønnet web-tjenesten for at referere til den nye erfarne model. Definitionen af web-tjenesten er en intern repræsentation af erfaren modellen af webtjenesten og kan ikke direkte ændres. Sørg for, at du henter definitionen af web-tjenesten for din skønnet forsøg og ikke kursus forsøg.

## <a name="sign-in-to-azure-resource-manager"></a>Log på til Azure ressourcestyring

Du skal først logge på din Azure-konto fra det PowerShell-miljø ved hjælp af [Tilføj AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

## <a name="get-the-web-service-definition-object"></a>Få objektet Web Service Definition

Få derefter objektet Web Service Definition ved at ringe til Cmdletten [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

For at bestemme ressource gruppenavn for en eksisterende webtjeneste skal du køre følgende Get-AzureRmMlWebService cmdlet uden parametre til at vise webtjenester i dit abonnement. Find webtjenesten, og derefter se på dens web service-ID. Navnet på ressourcegruppen er det fjerde element-id, lige efter *resourceGroups* elementet. I eksemplet nedenfor er gruppenavnet ressource standard-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Du kan også for at bestemme ressource gruppenavnet på en eksisterende webtjeneste, log på portalen Azure Machine Learning Web Services. Vælg webtjenesten. Ressource gruppenavn er det femte element af URL-adressen for webtjenesten, lige efter *resourceGroups* elementet. I eksemplet nedenfor er gruppenavnet ressource standard-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Eksportere objektet Web Service Definition som JSON

Hvis du vil ændre definitionen af erfaren modellen til brug af nyligt erfaren, skal du først bruge [Eksportér AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet til at eksportere til en fil i JSON-format.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Opdatere referencen til ilearner blob

Find [erfaren model] i aktiverne, opdatere værdien *uri* i noden *locationInfo* med URI ilearner blob. URI genereres ved at kombinere *BaseLocation* og *RelativeLocation* fra outputtet af BES omskoling opkald.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importere JSON til et Web Service Definition objekt

Du skal bruge [Importér AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet til at konvertere den ændrede JSON-fil i en Web Service Definition objekt, du kan bruge til at opdatere predicative forsøget igen.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Opdatere webtjenesten

Til sidst skal bruge [Opdatering AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet til at opdatere skønnet forsøget.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
