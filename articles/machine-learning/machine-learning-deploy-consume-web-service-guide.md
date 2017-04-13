<properties
    pageTitle="Azure Machine Learning-webtjenester: Installation og forbrug | Microsoft Azure"
    description="Ressourcer til implementering og forbrug webtjenester."
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
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning-webtjenester: Installation og forbrug

Du kan bruge Azure computer kan installere machine-learning-arbejdsprocesser og modeller som webtjenester. Disse webtjenester kan derefter bruges til at ringe machine-learning-modeller fra programmer via internettet til at gøre prognoser i realtid eller i batch-tilstand. Da webtjenesterne RESTful, kan du ringe til dem fra forskellige programming sprog og platforme, som .NET og Java, og fra programmer, som Excel.

De næste afsnit indeholder links til gennemgange, kode og dokumentation for at komme i gang.

## <a name="deploy-a-web-service"></a>Installere en webtjeneste

### <a name="with-azure-machine-learning-studio"></a>Med Azure Machine Learning Studio

Machine Learning Studio og portalen Microsoft Azure Machine Learning Web Services hjælpe dig med at installere og administrere en webtjeneste uden at skrive kode.

Følgende links giver generelle oplysninger om, hvordan du kan installere en ny webtjeneste:

* Du kan finde et overblik over, hvordan du kan installere en ny webtjeneste, der er baseret på Azure ressourcestyring [Implementer en ny webtjeneste](machine-learning-webservice-deploy-a-web-service.md).
* Du kan finde en gennemgang om, hvordan du installerer en webtjeneste, [Implementer en Azure Machine Learning webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).
* Finde en fuld gennemgang om, hvordan du opretter og installerer en webtjeneste, [gennemgang trin 1: oprette et Machine Learning arbejdsområde](machine-learning-walkthrough-1-create-ml-workspace.md).
* Specifikke eksempler, der implementerer en webtjeneste, i:

    * [Gennemgang trin 5: Installere Azure Machine Learning-webtjeneste](machine-learning-walkthrough-5-publish-web-service.md)
    * [Hvordan du kan installere en webtjeneste til flere områder](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Med web services ressource udbyder API'er (Azure ressourcestyring API'er)

Azure Machine Learning ressource-provider til webtjenester aktiverer installation og administration af webtjenester ved hjælp af REST-API-opkald. Se [Machine Learning Web Service (RESTEN)](https://msdn.microsoft.com/library/azure/mt767538.aspx) få mere at vide om MSDN.

### <a name="with-powershell-cmdlets"></a>Med PowerShell-cmdlet'er

Azure Machine Learning ressource-provider til webtjenester aktiverer installation og administration af webtjenester ved hjælp af PowerShell-cmdlet'er.

Hvis du vil bruge-cmdletter, skal du først logge din Azure-konto fra det PowerShell-miljø ved hjælp af [Tilføj AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet. Hvis du ikke har kendskab til, hvordan du ringe til PowerShell-kommandoer, der er baseret på Ressourcestyring, kan du se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Brug [Denne eksempelkode](https://github.com/ritwik20/AzureML-WebServices)for at eksportere dine skønnet forsøg. Når du opretter .exe-filen fra koden, kan du skrive:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Køre programmet opretter en web service JSON-skabelon. Hvis du vil bruge skabelonen til at installere en webtjeneste, skal du tilføje følgende oplysninger:

* Kontonavn lager og nøgle

    Du kan få kontonavn lager og nøgle fra [Azure-portalen](https://portal.azure.com/) eller [Azure klassisk portal](http://manage.windowsazure.com/).
* Anvendelsen plan-ID

    Du kan få plan ID'ET fra portalen [Azure Machine Learning Web-tjenester](https://services.azureml.net) ved at logge på og klikke på en plannavnet.

Føje dem til skabelonen JSON som underordnede noden *Egenskaber* på det samme niveau som *MachineLearningWorkspace* node.

Her er et eksempel:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Se følgende artikler og eksempelkode for at få yderligere oplysninger:

* [Azure Machine Learning cmdletter]( https://msdn.microsoft.com/library/azure/mt767952.aspx) reference på MSDN
* Eksempel [gennemgang](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) på GitHub

## <a name="consume-the-web-services"></a>Bruge webtjenesterne

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Fra Azure Machine Learning webtjenester Brugergrænsefladen (test)

Du kan teste din webtjeneste fra portalen Azure Machine Learning Web Services. Dette omfatter test tjenesten anmodning om svar (Ressourceposter) og grænseflader i batchen udførelse af service (BES).

* [Installere en ny webtjeneste](machine-learning-webservice-deploy-a-web-service.md)
* [Installere en Azure Machine Learning-webtjeneste](machine-learning-publish-a-machine-learning-web-service.md)
* [Gennemgang trin 5: Installere Azure Machine Learning-webtjeneste](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Fra Excel

Du kan hente en Excel-skabelon, der forbruger webtjenesten:

* [Forbrug en Azure Machine Learning webtjeneste fra Excel](machine-learning-consuming-from-excel.md)
* [Excel-tilføjelsesprogrammet til Azure Machine Learning Web Services](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>Fra en RESTEN-baseret klient

Azure Machine Learning webtjenester er RESTful API'er. Du kan bruge disse API'er fra forskellige platforme, som .NET, Python, R, Java osv. Siden **forbrug** for webtjenesten på [Microsoft Azure Machine Learning webtjenester portal](https://services.azureml.net) har eksempelkode, der kan hjælpe dig med at komme i gang. Se, [hvordan du bruge en Azure Machine Learning webtjeneste, der er blevet installeret fra en computer, læ forsøg](machine-learning-consume-web-services.md)kan finde flere oplysninger.

