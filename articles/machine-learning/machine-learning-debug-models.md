<properties 
    pageTitle="Fejlfinding af din Model i Azure Machine Learning | Microsoft Azure" 
    description="Forklarer, hvordan du hvordan du udfører fejlfinding din Model i Azure Machine Learning." 
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
    ms.date="09/09/2016" 
    ms.author="bradsev;garye" />

# <a name="debug-your-model-in-azure-machine-learning"></a>Fejlfinding af din Model i Azure Machine Learning

Denne artikel forklares af, hvordan du fejlfinding af din-modeller i Microsoft Azure Machine Learning. Præcist dækker den af mulige årsager, hvorfor en af følgende to situationer kan opstå, når du kører en model:

* [Tog Model] [ train-model] modul opstår en fejl 
* [Resultat Model] [ score-model] modul giver forkerte resultater 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-throws-an-error"></a>Tog Model modul opstår en fejl

![image1](./media/machine-learning-debug-models/train_model-1.png)

[Tog Model] [ train-model] modul forventer, at de følgende 2 input:

1. Hvilke typer klassifikation/Regression Model fra samling af modeller, der leveres af Azure Machine Learning
2. Kursus data med en bestemt etiket kolonne. Kolonnen navn angiver variabel til at forudsige. Resten af de kolonner med antages for at være funktioner.

Dette modul opstår en fejl i følgende tilfælde:

1. Kolonnen navn er angivet forkert, enten fordi mere end én kolonne er markeret som etiket eller en forkert kolonneindeks er markeret. Det andet tilfælde vil for eksempel gælder, hvis et kolonneindeks 30 blev brugt med en input dataset, der havde kun 25 kolonner.

2. Datasættet indeholder ikke en funktion kolonner. Eksempelvis hvis input datasættet har kun 1 kolonne, der er markeret som kolonnen navn, er der ingen funktioner, som du opretter modellen. I dette tilfælde [Tog Model] [ train-model] modul vil udløse en fejl.

3. Indtast datasættet (funktioner eller etiket) indeholder uendeligt som en værdi.


## <a name="score-model-module-does-not-produce-correct-results"></a>Resultat Model modul medfører ikke korrekte resultater

![image2](./media/machine-learning-debug-models/train_test-2.png)

Kontrolleret læ, de [Opdelte Data] i en typisk kursus/test graf for[ split] modul opdeler det oprindelige datasæt i to dele: den del, der bruges til at undervise modellen og den del, der er reserveret for at få hvor godt erfaren modellen udfører for de data, den ikke undervise på. Erfaren modellen bruges derefter til at give den testdata, hvorefter resultaterne evalueres for at afgøre nøjagtigheden af modellen.

[Resultat Model] [ score-model] modul kræver to input:

1. En erfaren model output fra [Tog Model] [ train-model] modul
2. En vurdering datasæt ikke, modellen ikke er accepteret på

Det kan opstå, selvom forsøget lykkes, [Karakteren Model] [ score-model] modul giver forkerte resultater. Flere situationer kan medføre, at dette sker:

1. Hvis den angivne etiket er kategoriske og en regressionsmodel oplæring i dataene, en forkert udskriftsoutput frembringes af [Karakteren Model] [ score-model] modul. Dette skyldes, at regression kræver en fortløbende svar variabel. I dette tilfælde skal det være mere passende at bruge en klassifikation model. 
2. På samme måde, hvis en klassifikation model oplæring i et datasæt, har du flydende tal i kolonnen navn, kan det medføre uønskede resultater. Dette skyldes, at klassifikation kræver en dedikeret svar variabel, som kun tillader værdier området over en endelig og normalt smule mindre sæt klasser.
3. Hvis vurdering datasættet ikke indeholder alle de funktioner, der bruges til at undervise modellen, [Karakteren Model] [ score-model] vil returnere en fejl.
4. [Resultat Model] [ score-model] vil ikke blive vist noget output svarer til en række i den vurdering datasæt, der indeholder en manglende værdi eller en uendelig værdi for et af de tilgængelige funktioner.
5. [Resultat Model] [ score-model] kan give identiske afgang for alle rækker i vurdering datasættet. Dette kan ske, for eksempel i sektionen når forsøger klassifikation ved hjælp af beslutning områder, hvis det mindste antal eksempler per bladnode er valgt at være mere end antallet kursus eksempler, der er tilgængelige.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 
