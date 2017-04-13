<properties
    pageTitle="Henblik på omstilling en ny webtjeneste ved hjælp af Machine Learning Management PowerShell-cmdlet'er | Microsoft Azure"
    description="Lær, hvordan du automatisk henblik på omstilling en model og opdatere webtjeneste for at bruge den nyligt erfaren model i Azure Machine Learning ved hjælp af Machine Learning Management PowerShell-cmdletterne."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Henblik på omstilling en ny webtjeneste ved hjælp af Machine Learning Management PowerShell-cmdletterne

Når du henblik på omstilling en ny webtjeneste, kan du opdatere definitionen af skønnet web-tjenesten for at referere til den nye erfarne model.  

## <a name="prerequisites"></a>Forudsætninger

Du skal har konfigureret et kursus forsøg og en skønnet forsøg som vist i [henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] Skønnet forsøget skal installeres som en Azure ressourcestyring (ny) baseret machine learning-webtjeneste. 
 
Du kan finde flere oplysninger om installere webtjenester skal [Implementer en Azure Machine Learning webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).

Denne proces kræver, at du har installeret Azure Machine Learning cmdletter. Installere Machine Learning cmdletter kan finde oplysninger [Azure Machine Learning cmdletter](https://msdn.microsoft.com/library/azure/mt767952.aspx) referencen på MSDN.

Følgende oplysninger har kopieret fra retraining output:

* BaseLocation
* RelativeLocation

De trin, du tager, er:

1.  Log på kontoen Azure ressourcestyring.
2.  Få definitionen af web-tjenesten
3.  Eksportere definitionen af Web-tjenesten som JSON
4.  Opdater referencen til ilearner blob i JSON.
5.  Importere JSON til en Web Service Definition
6.  Opdatere webtjenesten med definitionen af nye Web-tjenesten

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Log på kontoen Azure ressourcestyring

Du skal først logge på din Azure-konto fra det PowerShell-miljø ved hjælp af [Tilføj AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet.

## <a name="get-the-web-service-definition"></a>Få definitionen af Web-tjenesten

Dernæst skal få webtjenesten ved at ringe til Cmdletten [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . Definitionen af Web-tjenesten er en intern repræsentation af erfaren modellen af webtjenesten og kan ikke direkte ændres. Sørg for, at du henter definitionen af Web-tjenesten for din skønnet forsøg og ikke kursus forsøg.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

For at bestemme ressource gruppenavn for en eksisterende webtjeneste skal du køre følgende Get-AzureRmMlWebService cmdlet uden parametre til at vise webtjenester i dit abonnement. Find webtjenesten, og derefter se på dens web service-ID. Navnet på ressourcegruppen er det fjerde element-id, lige efter *resourceGroups* elementet. I eksemplet nedenfor er gruppenavnet ressource standard-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Du kan også for at bestemme ressource gruppenavnet på en eksisterende webtjeneste, log på portalen Microsoft Azure Machine Learning Web Services. Vælg webtjenesten. Ressource gruppenavn er det femte element af URL-adressen for webtjenesten, lige efter *resourceGroups* elementet. I eksemplet nedenfor er gruppenavnet ressource standard-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Eksportere definitionen af Web-tjenesten som JSON

Redigere definitionen erfaren modellen med at bruge den nyligt erfaren modellen, skal du først bruge cmdlet'en [Eksportér AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) til at eksportere til en JSON-filformatet.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Opdater referencen til ilearner blob i JSON.

Find [erfaren model] i aktiverne, opdatere værdien *uri* i noden *locationInfo* med URI ilearner blob. URI genereres ved at kombinere *BaseLocation* og *RelativeLocation* fra outputtet af BES omskoling opkald.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importere JSON til en Web Service Definition

Du skal bruge [Importér AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet til at konvertere den ændrede JSON-fil i en Web Service Definition, du kan bruge til at opdatere Predicative forsøget igen.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Opdatere webtjenesten med definitionen af nye Web-tjenesten

Til sidst skal bruge du [Opdatering AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet til at opdatere skønnet forsøget.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Oversigt

Ved hjælp af den maskine Learning PowerShell-cmdletter til administration, kan du opdatere erfaren modellen af en skønnet webtjeneste aktivere scenarier f.eks.:

* Periodiske model omskoling med nye data.
* Fordeling af en model til kunder med formålet med at lade dem henblik på omstilling modellen ved hjælp af deres egne data.
