<properties
    pageTitle="Hvad skete der med mine WebJob projekt (Visual Studio Azure-lager forbindelse service)? | Microsoft Azure"
    description="I denne artikel beskrives, Hvad skete der i et projekt med Azure WebJob, når forbindelsen til en lagerplads konto ved hjælp af Visual Studio forbundne tjenester"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Hvad skete der med mine WebJob projekt (Visual Studio Azure-lager forbindelse service)?

## <a name="references-added"></a>Referencer, der er tilføjet

Azure-lager NuGet pakken blev føjet til eller opdateret i dit Visual Studio-projekt.  
Denne pakke tilføjer følgende .NET referencer:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Forbindelsesstreng til Azure-lager, der er tilføjet
**AzureWebJobsStorage** og **AzureWebJobsDashboard** posterne er blevet opdateret med den valgte lagerplads konto forbindelsesstreng og nøgletal i filen App.config af projektet.

Du kan finde yderligere oplysninger finder [Azure WebJobs dokumentationsressourcer](http://go.microsoft.com/fwlink/?linkid=390226).
