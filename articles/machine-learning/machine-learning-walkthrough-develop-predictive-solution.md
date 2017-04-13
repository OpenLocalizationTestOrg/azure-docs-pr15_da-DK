<properties
    pageTitle="En skønnet løsning til kreditkontrol risiko med Machine Learning | Microsoft Azure"
    description="En detaljeret gennemgang, der viser, hvordan du opretter en skønnet analytics-løsning for kredit risikovurdering i Azure Machine Learning Studio."
    keywords="kredit risikoen, løsningen skønnet analytics, risikovurdering"
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
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Gennemgang: Udvikle en skønnet analytics-løsning for kredit risikovurdering i Azure Machine Learning

Antag, at du har brug for til at forudsige en persons kredit risikoen baseret på de oplysninger, de giver på et kredit-program.  

Kredit risikovurdering er en kompleks problem, selvfølgelig, men lad os forenkle parametrene for spørgsmålet lidt. Vi kan derefter bruge det som et eksempel på, hvordan du kan bruge Microsoft Azure Machine Learning med Machine Learning Studio og webtjenesten Machine Learning til at oprette sådan en skønnet analytics-løsning.  

I denne detaljeret gennemgang skal vi følge processen med at udvikle en skønnet analytics model i Machine Learning Studio og implementere det som en Azure Machine Learning webtjeneste. Vi starter med offentligt tilgængelige kredit risikoen data, udvikle og uddannelse af en skønnet model, der er baseret på dataene, og derefter udruller modellen som en webtjeneste, der kan bruges af andre til kreditkontrol risikovurdering.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] For at downloade og udskrive et diagram, der giver et overblik over funktionerne i Machine Learning Studio skal du se [Oversigtsdiagram over Azure Machine Learning Studio egenskaber](machine-learning-studio-overview-diagram.md).

Hvis du vil oprette en kreditkontrol risikoen vurdering løsning, får vi følge disse trin:  

1.  [Oprette et Machine Learning arbejdsområde](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Overføre eksisterende data](machine-learning-walkthrough-2-upload-data.md)
3.  [Oprette en ny forsøg](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Uddannelse og evaluerer modellerne](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Installere webtjenesten](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Få adgang til webtjenesten](machine-learning-walkthrough-6-access-web-service.md)

Denne gennemgang er baseret på en forenklet version af det [binære Classfication: kredit risikoen forudsigelse](http://go.microsoft.com/fwlink/?LinkID=525270) eksempel forsøg i [Cortana Intelligence galleriet](http://gallery.cortanaintelligence.com/).
