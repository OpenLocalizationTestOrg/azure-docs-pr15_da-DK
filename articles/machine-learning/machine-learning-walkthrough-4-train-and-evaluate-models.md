<properties
    pageTitle="Trin 4: Undervise og evaluerer skønnet analytisk modeller | Microsoft Azure"
    description="Trin 4 i udarbejde en skønnet løsning gennemgang: tog, resultat og evaluerer flere modeller i Azure Machine Learning Studio."
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


# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Gennemgang trin 4: Undervise og evaluerer skønnet analytisk modeller

Dette emne indeholder det fjerde trin i denne gennemgang, [udvikle en skønnet analytics-løsning i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Oprette et Machine Learning arbejdsområde](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Overføre eksisterende data](machine-learning-walkthrough-2-upload-data.md)
3.  [Oprette en ny forsøg](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Uddannelse og evaluerer modellerne**
5.  [Installere webtjenesten](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Få adgang til webtjenesten](machine-learning-walkthrough-6-access-web-service.md)

----------

En af fordelene ved at bruge Azure maskine læ Studio til oprettelse af machine learning modeller er muligheden for at prøve mere end én type model ad gangen i et forsøg og sammenligning af resultaterne. Denne type forsøg hjælper dig med at finde den bedste løsning til dit problem.

I forsøget Vi udvikler i denne gennemgang, skal vi oprette to forskellige typer modeller og derefter sammenligne deres vurdering resultater for at afgøre, hvilken algoritme vi vil bruge i vores endelige forsøg.  

Der findes forskellige modeller vi kunne vælge mellem. Udvid noden **Machine Learning** i paletten modul for at få vist de tilgængelige modeller, og udvid derefter **Initialiseret Model** og noder nedenunder. Med henblik på dette forsøg vælger vi Support vektor maskine (SVM) og de to klasse øget beslutningstræer moduler.    

> [AZURE.TIP] For at få hjælp til at bestemme, hvilke Machine Learning algoritme bedst passer til det specifikke problem forsøger du at løse, se, [hvordan du vælger algoritmer til Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Uddannelse af modellerne
Først skal Lad os konfigurere øget beslutning træet model:  

1.  Finde [To klasse øget beslutningstræ] [ two-class-boosted-decision-tree] modul i paletten modul og træk den til lærredet.
2.  Finde [Tog Model] [ train-model] modul, træk det over på lærredet, og forbind output fra modulet øget beslutning træet til den venstre input port ("baseret model") af [Tog Model] [ train-model] modul.
    
    [To-klasse øget beslutningstræ] [ two-class-boosted-decision-tree] modul starter generisk modellen og [Oplære Model] [ train-model] bruger kursus data til at undervise modellen. 
     
3.  Oprette forbindelse venstre output ("resultatet Dataset") fra venstre [Udføre R Script] [ execute-r-script] modul til højre input port ("Dataset") af [Tog Model] [ train-model] modul.

    > [AZURE.TIP] Vi ikke har brug for to af materialer og én for afgang af [Udføre R Script] [ execute-r-script] -modul til dette forsøg, så vi lade dem ikke-tilknyttet. 

4.  Vælg den [Tog Model] [ train-model] modul. Klik på **Start kolonne Datavælger**i ruden **Egenskaber** , Vælg **Alle typer** i rullemenuen ned under **Tilgængelige kolonner** , og Angiv "Kredit risikoen" i tekstfeltet. Vælg **Alle typer** i rullemenuen under **Markerede kolonner**. Vælg "Kredit risikoen", og klik på knappen med den fremhævede pil for at flytte den til **Markerede kolonner**. 
5.  Klik på **Gem**.


Denne del af forsøget nu ser nogenlunde således ud:  

![Kursus en model][1]

Dernæst skal konfigurere vi SVM modellen.  

Første, en lille forklaring om SVM. Øget beslutningstræer fungerer fint med funktioner i en hvilken som helst type. Da modulet SVM genererer en lineær klassificering, har den model, der oprettes dog fejlen bedste test når alle numeriske funktioner har det samme målestoksforhold. Hvis du vil konvertere alle numeriske funktioner til det samme målestoksforhold, skal vi bruge en "Tanh" transformation (med [Normalisere Data] [ normalize-data] modul.) Dette omdanner vores tal til området [0,1]. Streng funktioner konverteres af modulet SVM til kategoriske funktioner og derefter til binær 0/1-funktionerne, så vi ikke behøver at transformere manuelt streng funktioner. Også vi vil ikke transformere kredit risikoen kolonne (kolonne 21) – det er numeriske, men det er den værdi, vi kursus modellen til at forudsige, så vi har brug for at lade den stå alene.  

For at konfigurere SVM modellen skal du gøre følgende:

1.  Finde [To klasse Support vektor maskine] [ two-class-support-vector-machine] modul i paletten modul og træk den til lærredet.
2.  Højreklik på [Tog Model] [ train-model] modul, Vælg **Kopier**, og højreklik på lærredet, og vælg **Sæt ind**. Kopien af [Tog Model] [ train-model] modulet har samme kolonnemarkeringen som originalen.
3.  Oprette forbindelse output fra modulet SVM til den venstre input port ("baseret model") på den anden [Tog Model] [ train-model] modul.
4.  Finde [Normalisere Data] [ normalize-data] modul og træk den til lærredet.
5.  Oprette forbindelse input fra dette modul til venstre output fra venstre [Udføre R Script] [ execute-r-script] modul (Bemærk, at outputporten af et modul kan have forbindelse til mere end én andre modul).
6.  Oprette forbindelse venstre outputport ("transformeret datasæt") [Normalisere Data] [ normalize-data] modul til højre input port ("datasæt") af den anden [Tog Model] [ train-model] modul.
7.  I ruden **Egenskaber** for [Normalisere Data] [ normalize-data] modul, Vælg **Tanh** for parameteren **Transformation metode** .
8.  Klik på **Start kolonne Datavælger**, Vælg "Ingen kolonner" for **Begynder med**, Markér **Medtag** i den første rulleliste, Vælg **Kolonnetype** på den anden rullelisten, og vælg **numerisk** i den tredje rulleliste. Dette angiver, at alle de numeriske kolonner (og kun numeriske) transformeret.
9.  Klik på plustegnet (+) til højre for denne række – dette opretter en række med rullemenuerne. Vælg **udelade** i den første rulleliste, Vælg **kolonnenavne** i anden rullemenuen, og klik på tekstfeltet, og vælg "Kredit risikoen" fra listen over kolonner. Dette angiver, at kolonnen kredit risikoen skal ignoreres (vi skal gøre dette, da denne kolonne er numeriske, og så ellers ville være transformeret).
10. Klik på **OK**.  


[Normalisere Data] [ normalize-data] modul nu er indstillet til at udføre en Tanh transformation på alle numeriske kolonner med undtagelse af kolonnen kredit risikoen.  

Denne del af vores forsøg bør nu se ud som følger:  

![Kursus anden modellen][2]  

##<a name="score-and-evaluate-the-models"></a>Resultat og evaluerer modellerne

Vi bruger datatypen test, der er blevet adskilt af de [Opdelte Data] [ split] modul til at give vores erfaren modeller. Vi kan derefter sammenligne resultaterne af de to modeller at se, som genereres bedre resultater.  

1.  Finde [Karakteren Model] [ score-model] modul og træk den til lærredet.
2.  Oprette forbindelse [Tog Model] [ train-model] modul, der er forbundet med [To klasse øget beslutningstræ] [ two-class-boosted-decision-tree] modul til venstre input port [Karakteren Model] [ score-model] modul.
3.  Oprette forbindelse til den rigtige input port [Karakteren Model] [ score-model] modulet til venstre output fra højre [Udføre R Script] [ execute-r-script] modul.

    [Resultat Model] [ score-model] modul kan nu kredit oplysningerne fra test dataene, køre gennem modellen og sammenligne prognoser modellen genererer med kolonnen faktisk kredit risikoen i test dataene.

4.  Kopiere og indsætte [Karakteren Model] [ score-model] modul til at oprette en anden kopi, eller træk et nyt modul til lærredet.
5.  Oprette forbindelse venstre input port i dette modul til SVM modellen (det vil sige, oprette forbindelse til outputporten [Tog Model] [ train-model] modul, der er forbundet med [To klasse Support vektor maskine] [ two-class-support-vector-machine] modul).
6.  For SVM modellen har vi gør det samme transformation til testdata, som vi har til uddannelsesdata. Kopiere og indsætte [Normalisere Data] så[ normalize-data] modul til at oprette en anden kopi og forbinde den til venstre output fra højre [Udføre R Script] [ execute-r-script] modul.
7.  Oprette forbindelse til den rigtige input port [Karakteren Model] [ score-model] modul til venstre output [Normalisere Data] [ normalize-data] modul.  

Hvis du vil evaluere de to vurdering resultater, skal vi bruge en [Evaluere Model] [ evaluate-model] modul.  

1.  Finde [Evaluere Model] [ evaluate-model] modul og træk den til lærredet.
2.  Slutte den venstre input port til outputporten [Karakteren Model] [ score-model] modul, der er knyttet til øget beslutning træet modellen.
3.  Oprette forbindelse højre input port til den anden [Karakteren Model] [ score-model] modul.  

Hvis du vil køre forsøget, skal du klikke på knappen **Kør** under lærredet. Det kan tage et par minutter. En rundt om indikator på hvert modul viser, at den kører, og et grønt flueben, når modulet, der er afsluttet. Når alle moduler har en markering, er forsøget afsluttet.

Forsøget bør nu se nogenlunde sådan ud:  

![Evaluering af begge modeller][3]

Klik på outputporten [Evaluere Model] for at kontrollere resultaterne,[ evaluate-model] modul, og vælg **visuelle effekter**.  

[Evaluere Model] [ evaluate-model] modul giver et par kurver og mål, som gør det muligt at sammenligne resultaterne af de to scored modeller. Du kan få vist resultaterne, som modtager Operator egenskab (ROC) kurver, præcision/tilbagekaldelsen kurver eller elevator kurver. Yderligere data vises omfatter en forvirring matrix, akkumulerede værdier for området under kurven (AUC) og anden metrik. Du kan ændre grænseværdien ved at flytte skyderen mod venstre eller højre og se, hvordan det påvirker sæt af målepunkter.  

Til højre for diagrammet, skal du klikke på **Scored dataset** eller **Scored datasæt til at sammenligne** for at fremhæve tilknyttede kurven og få vist de tilknyttede målepunkter nedenfor. I forklaringen til kurver, "Karakter datasæt" svarer til den venstre input port [Evaluere Model] [ evaluate-model] modul – i dette tilfælde dette er øget beslutning træet modellen. "Karakter dataset for at sammenligne" svarer til den rigtige input port - SVM modellen i dette tilfælde. Når du klikker på en af disse etiketter, er fremhævet kurven for modellen, og få vist den tilsvarende målepunkter, som vist i følgende illustration.  

![ROC kurver for modeller][4]

Ved at undersøge disse værdier, kan du bestemme, hvilken der er tættest på hvorved du får de resultater, du leder efter. Du kan gå tilbage og forbedrer din forsøg ved at ændre værdierne i de forskellige modeller. 

> [AZURE.TIP] Hver gang du kører forsøget en oversigt over gentagelsen der bevares i oversigten køre. Du kan få vist disse gentagelser og vende tilbage til enhver af dem, ved at klikke på **Få vist HISTORIKKEN for køre** under lærredet. Du kan også klikke på **Forudgående køre** i ruden **Egenskaber for** at vende tilbage til den gentagelse, der er umiddelbart før du har åbnet.
> 
Du kan oprette en kopi af en gentagelse af din forsøg ved at klikke på **Gem som** under lærredet. Bruge den forsøg **Oversigt** og en **Beskrivelse** egenskaber til at holde styr på, hvad du har prøvet i din forsøg gentagelser.

>  Få mere at vide ved at se [Administrer eksperimentere gentagelser i Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).  


----------

**Næste: [Implementer webtjenesten](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
