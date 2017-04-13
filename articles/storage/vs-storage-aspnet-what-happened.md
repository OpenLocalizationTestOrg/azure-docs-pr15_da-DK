<properties
    pageTitle="Hvad skete der med mine ASP.NET-projekt? | Microsoft Azure | Visual Studio forbundne tjenester"
    description="I denne artikel beskrives, hvad sker der, efter at føje Azure-lager til en ASP.NET-projekt, ved hjælp af Visual Studio forbundne tjenester"
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

# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Hvad skete der med mine ASP.NET-projekt (Visual Studio Azure-lager forbindelse service)?

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

##<a name="connection-string-for-azure-storage-added"></a>Forbindelsesstreng til Azure-lager, der er tilføjet
Et element blev oprettet med den valgte lagerplads konto forbindelsesstreng og nøgle i filen web.config af projektet.

Du kan finde flere oplysninger [ASP.NET](http://www.asp.net).
