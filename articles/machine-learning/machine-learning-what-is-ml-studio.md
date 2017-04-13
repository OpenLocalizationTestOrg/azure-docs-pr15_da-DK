<properties 
    pageTitle="Hvad er Azure Machine Learning Studio? | Microsoft Azure"
    description="Oversigt over Azure ML Studio, et træk og slip værktøj til hurtig opbygning af modeller fra et klar til brug bibliotek med algoritmer og moduler."
    keywords="Azure maskine læ, azure ml ml studio"
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
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>Hvad er Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio er et samarbejde, træk og slip værktøj, du kan bruge til at oprette, teste og implementere løsninger skønnet analyser af dine data. Machine Learning Studio publicerer modeller som webtjenester, der nemt kan benyttes af brugerdefinerede apps eller BI-værktøjer, som Excel.

Machine Learning Studio er, hvor data videnskabelige, skønnet analytics, skyen ressourcer og dine data opfylder.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Arbejdsområdet Machine Learning Studio interaktive

For at udvikle en skønnet analyse model, du typisk bruge data fra en eller flere kilder, transformere og analysere dataene via forskellige data strengmanipulation og statistiske funktioner og oprette et sæt af resultater. Udvikle en model som dette er en gentaget proces. Når du ændrer de forskellige funktioner og deres parametre, konvergerer dine resultater, indtil du er tilfreds, at du har en erfaren og effektive model.

**Azure Machine Learning Studio** giver dig et interaktivt, visuel arbejdsområde til nemt at opbygge, teste og forbedrer en skønnet analyse model. Du træk og slip ***datasæt*** og analyse ***moduler*** til en interaktiv lærred, forbinde dem sammen for at danne en ***eksperimentere***, som du kan køre i Machine Learning Studio. Gemme en kopi, hvis det er nødvendigt for at forbedrer din modeldesign, du redigerer forsøget, og kør den igen. Når du er klar, kan du konvertere din ***kursus eksperimentere*** til en ***skønnet forsøg***og derefter publicere den som en ***webtjeneste*** , så du kan få adgang til din model af andre.

>[AZURE.TIP] For at downloade og udskrive et diagram, der giver et overblik over funktionerne i Machine Learning Studio skal du se [Oversigtsdiagram over Azure Machine Learning Studio egenskaber](machine-learning-studio-overview-diagram.md).

Der er ingen programmering påkrævet, lige visuelt forbinde datasæt og moduler til at opbygge din skønnet analyse model.

![Azure ML Studio diagram: Opret forsøg, læse data for mange kilder, skrive karakter for data, Skriv modeller.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Komme i gang med Machine Learning Studio

Når du først skrive [Machine Learning Studio](https://studio.azureml.net) kan du se siden **Startside** . Her kan du få vist dokumentation, videoer, webinarer og finde andre værdifulde ressourcer.

Der er tre faner øverst: **Startside** (hvor du starter), **Studio**og **galleriet**.

### <a name="studio"></a>Studio

Klik på fanen **Studio** , og du bliver bedt om at logge på med din Microsoft-konto eller kontoen arbejds- eller skolekonto. Når du er logget, får du vist følgende faner i venstre side:

- **Projekter** - samlinger af forsøg, datasæt, notesbøger og andre ressourcer, der repræsenterer et enkelt projekt
- **Forsøg** - forsøg, der er oprettet, køre, og som er gemt som kladder
- **WEBTJENESTER** - webtjenester, som du har installeret fra din forsøg
- **NOTESBØGER** - Jupyter notesbøger, som du har oprettet
- **DATASÆT** - datasæt, du har overført til Studio
- **ERFAREN modeller** - modeller, som du har uddannelse til forsøg og gemt i Studio
- **Indstillinger** – en samling af indstillinger, du kan bruge til at konfigurere din konto og ressourcer.

### <a name="gallery"></a>Galleriet

Klik på fanen **Galleri** , og du vil blive ført til galleriet Cortana Intelligence. Galleriet er et sted, hvor en gruppe af data forskere og udviklere kan dele løsninger, der er oprettet ved hjælp af komponenterne i den Cortana Intelligence pakke.

Finde flere oplysninger om galleriet [del og Find løsninger i galleriet Cortana Intelligence](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Komponenterne i et forsøg

Et forsøg består af datasæt, der leverer data til analytical moduler, som du opretter forbindelse sammen til at opbygge en skønnet analyse model. Nærmere betegnet kan har en gyldig forsøg disse egenskaber:

- Forsøget har mindst én datasæt og et modul
- Datasæt kan forbindes kun til moduler
- Moduler kan have forbindelse til enten datasæt eller andre moduler
- Alle input porte for moduler skal have nogle forbindelse til dataflow
- Alle de nødvendige parametre for hvert modul skal angives

Du kan oprette et forsøg fra bunden, eller du kan bruge eksisterende eksempel forsøg som en skabelon. Du kan finde yderligere oplysninger finder [Brug eksempel forsøg at oprette nye forsøg](machine-learning-sample-experiments.md).

Et eksempel på oprettelse af en simpel forsøg, under [oprette en simpel forsøg i Azure Machine Learning Studio](machine-learning-create-experiment.md).

Du kan finde en mere komplet gennemgang af oprettelse af en skønnet analytics-løsning, [udvikle en skønnet løsning med Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Datasæt

Et datasæt er data, der er blevet overført til Machine Learning Studio, så den kan bruges i modelleringsprocessen. Et antal eksempel datasæt medfølger Machine Learning Studio for dig at eksperimentere med, og du kan overføre flere datasæt, som du vil have dem. Her er nogle eksempler på inkluderet datasæt:

- **MPG data for forskellige biler** - meter i gallon (MPG) værdier for biler, der er identificeret med antallet af flasker, hestekræfter osv.
- **Bryst kræft data** - bryst kræft diagnosticering data.
- **Skov udløses data** - skov fire størrelser i nordøst Portugal.

Når du opretter et forsøg kan du vælge fra listen over tilgængelige datasæt til venstre for lærredet.

Se en liste over eksempel datasæt, der er inkluderet i Machine Learning Studio, [bruge eksempel datasættene i Azure Machine Learning Studio](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Moduler

Et modul er en algoritme, som du kan udføre på dine data. Machine Learning Studio har et antal moduler, lige fra data vandindtrængen funktioner til vundne og valideringsprocesser. Her er nogle eksempler på inkluderet moduler:

- [Konvertere til ARFF] [ convert-to-arff] -konverterer et .NET Serienummereret datasæt til attribut-Relation fil Format (ARFF).
- [Beregne grundlæggende statistik] [ elementary-statistics] -beregner grundlæggende statistik som middelværdi, standardafvigelsen osv.
- [Lineær Regression] [ linear-regression] -opretter en online graduering afstamning-baserede lineær regressionsmodel.
- [Resultat Model] [ score-model] -Scores en erfaren klassifikation eller regression model.

Når du opretter et forsøg kan du vælge fra listen over tilgængelige moduler til venstre for lærredet.  

Et modul muligvis et sæt af parametre, du kan bruge til at konfigurere det modul interne algoritmer. Når du vælger et modul på lærredet, vises det modul parametre i ruden **Egenskaber** til højre for lærredet. Du kan ændre parametrene i ruden for at finjustere din model.

For nogle Hjælp navigere gennem det store bibliotek med machine learning algoritmer, der er tilgængelig, se, [hvordan du vælge algoritmer til Microsoft Azure maskine læ](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementere en skønnet analytics-webtjeneste

Når din skønnet analytics-model er klar, kan du installere det som en webtjeneste direkte fra Machine Learning Studio. Du kan finde flere oplysninger om denne proces, [Implementer en Azure Machine Learning webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
