<properties
    pageTitle="Hvad skete der med mine ASP.NET-5-projekt (Visual Studio forbindelse services) | Microsoft Azure-lager"
    description="Beskriver, hvad der sker, når forbindelsen til en Azure lagerplads konto i Visual Studio ASP.NET-5-projekt ved hjælp af Visual Studio forbundne tjenester"
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

# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Hvad skete der med mine ASP.NET-5-projekt (Visual Studio Azure-lager forbindelse services)?

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

Desuden pakken NuGet **Microsoft.Framework.Configuration.Json** blev tilføjet.

## <a name="connection-string-for-azure-storage-added"></a>Forbindelsesstreng til Azure-lager, der er tilføjet
Et element blev oprettet med den valgte lagerplads konto forbindelsesstreng og nøgle i filen config.json af projektet.

Du kan finde yderligere oplysninger finder [ASP.NET 5](http://www.asp.net/vnext).
