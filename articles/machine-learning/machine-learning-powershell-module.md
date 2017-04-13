<properties
    pageTitle="PowerShell-modul til Machine Learning | Microsoft Azure"
    description="PowerShell-modulet til Azure Machine Learning er tilgængelig i offentlige eksempelvisning. Bruge PowerShell til at oprette og administrere arbejdsområder, forsøg, webtjenester og meget mere."
    keywords="eksperimentere, lineær regression machine learning algoritmer machine learning selvstudium, og skønnet modellering teknikker, data videnskabelige forsøg"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>PowerShell-modul til Microsoft Azure Machine Learning

PowerShell-modulet til Azure Machine Learning er et effektivt værktøj, der gør det muligt at bruge Windows PowerShell til at administrere arbejdsområder, forsøg, datasæt, web serivces og meget mere.

Du kan se i dokumentationen og hente modulet, sammen med den fulde kildekode på [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>Hvad er Machine Learning PowerShell-modulet?

Machine Learning PowerShell-modulet er en. Nettooversigt-baserede DLL-modul, der tillader dig at administrere fuldt Azure Machine Learning arbejdsområder, forsøg, datasæt, webtjenester og web service slutpunkter fra Windows PowerShell. Sammen med modulet, kan du hente den fulde kildekode som omfatter et cleanly adskilt [C# API-lag](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Dette betyder, at du kan referere til denne DLL-fil fra dit eget .NET projekt og administrere Azure Machine Learning via .NET kode. Desuden afhænger DLL underliggende REST API'er, kan du udnytte direkte fra din foretrukne klient.

## <a name="what-can-i-do-with-the-powershell-module"></a>Hvad kan jeg gøre med PowerShell-modulet?

Her er nogle af de opgaver, du kan udføre med denne PowerShell-modulet. Se den [fulde dokumentation](https://aka.ms/amlps) til disse og mange flere funktioner.

- Klargør et nyt arbejdsområde ved hjælp af en management-certifikat ([Ny AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Eksportere og importere en JSON-fil, der repræsenterer en forsøg graf ([Eksportér AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) og [Importere AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Køre et forsøg ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Oprette en webtjeneste af en skønnet forsøg ([Ny AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Oprette et nyt slutpunkt på en publiceret webtjeneste ([Tilføj AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Kalde Ressourceposter og/eller BES web service ark ([Aktiver AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) og [Aktiver AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Her er et hurtigt eksempel ved at bruge PowerShell til at køre et eksisterende forsøg:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Se denne artikel om ved hjælp af PowerShell-modulet til at automatisere en meget ofte-anmodes om en opgave til en mere i dybden use case: [oprette mange Machine Learning-modeller og web service slutpunkter fra én forsøg ved hjælp af PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Hvordan kommer jeg i gang?

Hent den [Slip pakke](https://github.com/hning86/azuremlps/releases) fra GitHub for at komme i gang med Machine Learning PowerShell, og følg [instruktionerne til installation](https://github.com/hning86/azuremlps/blob/master/README.md). Du skal fjerne blokeringen af hentet/pakket DLL-filen og derefter importere den til dit PowerShell-miljø. De fleste af cmdletter kræver, at du angiver arbejdsområde-ID, arbejdsområde godkendelse tokenet og den Azure region, arbejdsområdet i. Den nemmeste måde at give disse er gennem et standard config.json fil, som er beskrevet i detaljer i installationsvejledningen. Naturligvis kan du også klone træet ciffer og ændre/kompilering af koden lokalt ved hjælp af Visual Studio.

## <a name="next-steps"></a>Næste trin

PowerShell-modulet fortsætter med at være forbedret og udvidet periode denne preview. Holde øje med [Cortana Intelligence og Machine Learning Blog](https://blogs.technet.microsoft.com/machinelearning/) for flere nyheder og oplysninger.
