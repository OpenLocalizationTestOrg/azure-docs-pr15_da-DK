<properties
    pageTitle="Trin 6: Få adgang til tjenesten Machine Learning web | Microsoft Azure"
    description="Trin 6 i udarbejde en skønnet løsning gennemgang: få adgang til en aktiv Azure Machine Learning-webtjeneste."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Gennemgang trin 6: Få adgang til Azure Machine Learning webtjenesten

Dette er det sidste trin i denne gennemgang, [udvikle en skønnet analytics-løsning i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Oprette et Machine Learning arbejdsområde](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Overføre eksisterende data](machine-learning-walkthrough-2-upload-data.md)
3.  [Oprette en ny forsøg](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Uddannelse og evaluerer modellerne](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Installere webtjenesten](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Få adgang til webtjenesten**

----------

I det forrige trin i denne gennemgang installeret vi en webtjeneste, der bruger vores kredit risikoen forudsigelse model. Nu skal brugerne skal kunne sende data til den og modtage resultater. 

Webtjenesten er en Azure-webtjeneste, der kan modtage og returnere data ved hjælp af REST API'er på en af to måder:  

-   **Anmodning/svar** - brugeren sender en eller flere rækker af kredit data til tjenesten ved hjælp af en HTTP-protokollen og svarer tjenesten med et eller flere sæt af resultater.
-   **Udførelse af batchen** - brugeren gemmer en eller flere rækker af kredit data i en Azure blob og sender derefter blob-placering til tjenesten. Tjenesten scores alle rækker med data i input blob, gemmer resultatet i en anden blob og returnerer URL-adressen i den pågældende beholder.  

Den hurtigste og nemmeste måde at få adgang til webtjenesten er gennem de Web App-skabeloner, der er tilgængelige i [Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).
Skabelonerne web app kan oprette en brugerdefineret WebApp, der kender din webtjeneste indtastede data, og hvad den returnerer. Alt, du skal gøre, er giver adgang til dine webtjeneste og data, og skabelonen gør resten.

Du kan finde flere oplysninger om brug af web app-skabeloner, [forbrug en Azure Machine Learning webtjeneste med en web app-skabelon](machine-learning-consume-web-service-with-web-app-template.md).

Du kan også udvikle et brugerdefineret program til at få adgang til webtjenesten ved hjælp af starter kode, der er angivet for dig på R, C# og Python programmeringssprog.
Du kan finde udførlige oplysninger i [hvordan du kan bruge en Azure Machine Learning webtjeneste, der er blevet publiceret fra en computer, læ forsøg](machine-learning-consume-web-services.md).
