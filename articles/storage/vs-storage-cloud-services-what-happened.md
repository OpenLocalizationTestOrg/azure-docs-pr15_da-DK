<properties
    pageTitle="Hvad skete der med mine skyen tjenesten project? | Microsoft Azure | Visual Studio forbundne tjenester"
    description="I denne artikel beskrives, hvad sker der i et cloud services-projekt, når forbindelsen til en Azure lagerplads konto ved hjælp af Visual Studio forbundne tjenester"
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

# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Hvad skete der med mine cloud services-projekt (Visual Studio Azure-lager forbindelse service)?

## <a name="references-added"></a>Referencer, der er tilføjet

Azure-lager NuGet pakken blev føjet til dit Visual Studio-projekt.  
Denne pakke tilføjer følgende .NET referencer:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Forbindelsesstreng til Azure-lager, der er tilføjet
Elementer er oprettet med den valgte lagerplads konto forbindelsesstreng og nøgle. Ændringer er foretaget følgende filer:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**
