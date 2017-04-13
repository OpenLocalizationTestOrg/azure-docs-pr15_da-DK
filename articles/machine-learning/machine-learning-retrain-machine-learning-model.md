<properties
    pageTitle="Henblik på omstilling en maskine læ Model | Microsoft Azure"
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
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Henblik på omstilling en Machine Learning-Model

Som en del af processen med at operationalization af machine learning-modeller i Azure maskine læ, er din model uddannelse og gemt. Du derefter bruge det til at oprette en predicative webtjeneste. Webtjenesten kan derefter bruges i websteder, dashboards og mobilapps. 

Modeller, du opretter ved hjælp af Machine Learning er typisk ikke statisk. Som nye data bliver tilgængelig, eller når destination for API har deres egne data skal være retrained modellen. 

Omskoling kan forekomme ofte. Du kan automatisk med funktionen programmeringsmæssig omskoling API henblik på omstilling modellen ved hjælp af de omskoling API'er og opdatere webtjenesten med den nyligt erfaren model. 

Dette dokument beskrives retraining processen og viser, hvordan du bruger API'er omskoling.

## <a name="why-retrain-defining-the-problem"></a>Hvorfor henblik på omstilling: definere problemet  

Som en del af machine learning-kurser proces, er en model uddannelse ved hjælp af et sæt af data. Modeller, du opretter ved hjælp af Machine Learning er typisk ikke statisk. Som nye data bliver tilgængelig, eller når destination for API har deres egne data skal være retrained modellen.

I disse scenarier giver en programmeringsmæssig API en nem måde at tillade, at du eller destination for din API'er til at oprette en klient, der kan, på grundlag engangsomkostning eller normalt henblik på omstilling modellen ved hjælp af deres egne data. De kan derefter evaluere resultaterne af omskoling og opdatere Web service API for at bruge den nyligt erfaren model.

>[AZURE.NOTE] Hvis du har en eksisterende kursus forsøg og en ny webtjeneste, kan du se antal en eksisterende skønnet webtjeneste i stedet for at følge denne gennemgang, der er nævnt i følgende afsnit.

## <a name="end-to-end-workflow"></a>Til slut arbejdsproces 

Processen omfatter følgende komponenter: A kursus forsøg og en skønnet forsøg publicerede som en webtjeneste. Hvis du vil aktivere omskoling af en erfaren model, skal kursus forsøget publiceres som en webtjeneste med output fra en erfaren model. Dette giver API adgang til modellen for omskoling. 

De følgende fremgangsmåde gælder kun for både nye og klassisk Web services:

Oprette den indledende skønnet webtjeneste:

* Oprette et kursus forsøg
* Oprette en skønnet web forsøg
* Installere en skønnet webtjeneste

Henblik på omstilling webtjenesten:

* Opdatere kursus forsøg at tillade omskoling
* Installere retraining webtjenesten
* Brug batchen udførelse af tjenestekode at omskole modellen

En gennemgang af de foregående trin, du kan finde [henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md).

Hvis du har installeret en klassisk webtjeneste:

* Oprette et nyt slutpunkt på skønnet webtjenesten
* Få programrettelse URL-adresse og kode
* Brug programrettelse URL-adressen til at pege på nyt slutpunkt på retrained modellen 

Se [henblik på omstilling en klassisk webtjeneste](machine-learning-retrain-a-classic-web-service.md)for en gennemgang af de foregående trin.

Hvis du støder på problemer omskoling en klassisk webtjeneste, skal du se [fejlfinding af omskoling af en Azure Machine Learning klassisk-webtjeneste](machine-learning-troubleshooting-retraining-models.md).

Hvis du har installeret en ny webtjeneste:

* Log på kontoen Azure ressourcestyring
* Få definitionen af Web-tjenesten
* Eksportere definitionen af Web-tjenesten som JSON
* Opdatere referencen til den `ilearner` blob i JSON
* Importere JSON til en Web Service Definition
* Opdatere webtjenesten med definitionen af nye Web-tjenesten

Se [henblik på omstilling en ny webtjeneste ved hjælp af Machine Learning Management PowerShell-cmdletterne](machine-learning-retrain-new-web-service-using-powershell.md)for en gennemgang af de foregående trin.

Processen til konfiguration af omskoling til en klassisk webtjeneste omfatter følgende trin:

![Omskoling overblik over processen][1]

Processen til konfiguration af omskoling til en ny webtjeneste omfatter følgende trin:

![Omskoling overblik over processen][7]

## <a name="other-resources"></a>Andre ressourcer

- [Omskoling og opdatering af Azure maskine læring modeller med Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Oprette mange Machine Learning-modeller og web service slutpunkter fra én forsøg ved hjælp af PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- [AML omskoling modeller ved hjælp af API'er](https://www.youtube.com/watch?v=wwjglA8xllg) video viser, hvordan du henblik på omstilling Machine Learning modeller, der er oprettet i Azure Machine Learning ved hjælp af omskoling API'er og PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

