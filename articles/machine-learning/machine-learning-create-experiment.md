<properties
    pageTitle="Et simpelt eksperiment i Machine Learning Studio | Microsoft Azure"
    description="Denne machine learning selvstudium fører dig gennem et let data videnskabelige forsøg. Vi kan forudsige prisen på en bil, ved hjælp af en regression algoritme."
    keywords="eksperimentere, lineær regression, machine learning algoritmer, machine learning selvstudium, forudseende modellering teknikker, data naturvidenskabelige eksperimenter"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Machine learning Selvstudium: oprette din første data naturvidenskabelige eksperimenter i Azure Machine Learning Studio

Denne machine learning selvstudium fører dig gennem et let data videnskabelige forsøg. Vi vil oprette en lineær regressionsmodel, der forudsiger, at prisen på en bil, der er baseret på forskellige variabler, som gør og tekniske specifikationer. Hvis du vil gøre dette, bruger vi Azure Machine Learning Studio til at udvikle og navigere på en enkel forudseende analytics eksperimentere.

*Forudseende analytics* er en slags data videnskab, der bruger aktuelle data til at forudsige fremtidige udfald. Se et meget enkelt eksempel på forudseende analytics Data videnskab for begyndere video 4: [forudsige et svar med en simpel model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (kørsel: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Hvordan hjælper Machine Learning Studio?

Machine Learning Studio gør det nemt at oprette et eksperiment ved hjælp af træk og slip-moduler forprogrammerede forudseende modellering teknikker. For at køre dit eksperiment og forudsige et svar, skal du bruge Machine Learning Studio til at *oprette en model*, *tog modellen*, og *point og test modellen*.

Angiv Machine Learning Studio: [https://studio.azureml.net](https://studio.azureml.net). Hvis du har logget på Machine Learning Studio før, skal du klikke på **Log på her**. Ellers skal du klikke på **Tilmeld dig** og vælge mellem gratis og betalte muligheder.

Finde flere generelle oplysninger om Machine Learning Studio, [Hvad er Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>Fem trin til at oprette et eksperiment

I denne machine learning-selvstudium, skal du følge fem grundlæggende trin for at oprette et eksperiment i Machine Learning Studio for at oprette, træne og resultat af modellen:

- Oprette en model
    - [Trin 1: Hent data]
    - [Trin 2: Forbehandle dataene]
    - [Trin 3: Definere funktioner]
- Tog modellen
    - [Trin 4: Vælge og anvende en learning-algoritme]
- Resultat og test modellen
    - [Trin 5: Forudsige nye biler priser]

[Trin 1: Hent data]: #step-1-get-data
[Trin 2: Forbehandle dataene]: #step-2-preprocess-data
[Trin 3: Definere funktioner]: #step-3-define-features
[Trin 4: Vælge og anvende en learning-algoritme]: #step-4-choose-and-apply-a-learning-algorithm
[Trin 5: Forudsige nye biler priser]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>Trin 1: Hent data

Der er en række eksempel datasæt, der følger med Machine Learning Studio, som du kan vælge mellem, og du kan importere data fra mange kilder. I dette eksempel bruger vi eksempelfiler dataset, **bil prisdata (Raw)**.
Dette datasæt indeholder poster for et antal individuelle biler, herunder oplysninger om mærke, model, tekniske specifikationer og pris.

1. Starte et nyt eksperiment ved at klikke på **+ Ny** nederst i vinduet Machine Learning Studio, Vælg **EKSPERIMENTERE**, og vælg derefter **Tom eksperimentere**. Vælg eksperiment standardnavn øverst på lærredet, og Omdøb den til et sigende navn, for eksempel **bil pris forudsigelse**.

2. Lærredet er en palet af datasæt og moduler til venstre for forsøget. Type **bil** i søgefeltet øverst på denne palet til at finde dataset mærket **prisdata bil (Raw)**.

    ![Paletten søgning][screen1a]

3. Træk dataset til lærredet eksperiment.

    ![DataSet][screen1]

Hvis du vil se, hvordan dataene ser ud, klik på outputport i bunden af datasættet bil, og vælg derefter **visuelle effekter**.

![Modul outputport][screen1c]

Variabler i datasættet vises som kolonner, og hver forekomst af en bil, der vises som en række. Kolonnen yderst til højre (kolonne 26 og med titlen "pris") er variablen mål, vi forsøger at forudsige.

![DataSet visualisering][screen1b]

Luk visualiseringsvinduet ved at klikke på "**x**" i øverste højre hjørne.

## <a name="step-2-preprocess-data"></a>Trin 2: Forbehandle dataene

Et dataset kræver normalt nogle forbehandling før den kan analyseres. Du har måske bemærket de manglende værdier i kolonnerne i forskellige rækker. Disse mangler værdier skal renses, så modellen kan analysere dataene korrekt. I dette tilfælde skal vi fjerne alle de rækker, der har manglende værdier. Kolonnen **normaliserede tab** har desuden en stor del af manglende værdier, så vi kan udelukke kolonnen helt fra modellen.

> [AZURE.TIP] Rengøring af de manglende værdier fra inputdata er en forudsætning for at bruge de fleste af modulerne.

Først skal vi fjerne kolonnen **normaliserede tab** , og derefter skal vi fjerne enhver række, der mangler data.

1. Skriv **Vælg kolonner** i boksen Søg øverst på paletten modul til at søge efter [Vælg kolonner i Dataset] [ select-columns] modul og derefter trække den til forsøget lærred, og Tilslut den til outputporten for datasættet **prisdata bil (Raw)** . Dette modul giver os mulighed at vælge, hvilke kolonner med data, som vi ønsker skal medtages eller udelades i modellen.

2. Vælg [Vælg kolonner i Dataset] [ select-columns] modul, og klik på **Start kolonnevælger** i ruden **Egenskaber** .

    - Klik på venstre **med regler**
    - Klik på **alle kolonner**under **Begynder med**. Dette dirigerer [Vælg kolonner i Dataset] [ select-columns] at passere igennem alle kolonner (undtagen dem, vi er ved at udelade).
    - Vælg **Udelad** og **kolonnenavne**,-rullelister, og klik derefter på i tekstboksen. Der vises en liste over kolonner. Vælg **normaliserede tab**, og det føjes til tekstboksen.
    - Klik på knappen markeret (OK) for at lukke kolonnevælgeren.

    ![Vælg kolonner][screen3]

    **Vælg** kolonner i datasættet i egenskabsruden angiver, at det kommer igennem alle kolonner fra datasættet undtagen **normaliserede tab**.

    ![Vælg kolonner i Egenskaber for datasæt][screen4]

    > [AZURE.TIP] Du kan tilføje en kommentar til et modul ved at dobbeltklikke på modulet og indtastning af tekst. Dette kan hjælpe dig med at få et overblik hvad modulet gør i dit eksperiment. I så fald skal du dobbeltklikke på [Vælg kolonner i Dataset] [ select-columns] modul, og skriv kommentaren "udelukke normaliserede tab."

3. Træk [Ren manglende Data] [ clean-missing-data] modul til forsøget lærredet, og Tilslut den til [Vælg kolonner i Dataset] [ select-columns] modul. Vælg **Fjern hele rækken** under **rensning tilstand** for at rense dataene ved at fjerne rækker, der har manglende værdier i ruden **Egenskaber** . Dobbeltklik på modulet, og skriv kommentaren "Fjern manglende værdi rækker".

    ![Ren manglende Data egenskaber][screen4a]

4. Køre forsøget ved at klikke på **Kør** under forsøget lærredet.

Når forsøget er afsluttet, har alle moduler en grøn markering for at angive, at de blev afsluttet. Bemærk også **kører færdig** -status i øverste højre hjørne.

![Første forsøg køre][screen5]

Vi har gjort i forsøget på dette punkt blot rense dataene. Klik på venstre outputporten [Ren manglende Data] , hvis du vil have vist de rensede dataset,[ clean-missing-data] modul ("Cleaned dataset"), og vælg **visuelle effekter**. Bemærk, at kolonnen **normaliserede tab** findes ikke længere, og der er ingen manglende værdier.

Nu, hvor dataene er ren, er vi klar til at angive, hvilke funktioner, som vi skal bruge i forudseende modellen.

## <a name="step-3-define-features"></a>Trin 3: Definere funktioner

*Funktioner* i machine learning, er individuelle målbare egenskaber af noget du er interesseret i. Hver række repræsenterer en bil, og hver kolonne er en funktion af denne bil i vores dataset.

At finde en god kræver sæt funktioner til oprettelse af en forudseende model forsøg og viden om problemet, du vil løse. Nogle funktioner er bedre til at forudsige mål end andre. Nogle funktioner har også en tæt sammenhæng med andre funktioner (for eksempel by-mpg kontra vej mpg), så de ikke føjer mange nye oplysninger til modellen, og de kan fjernes.

Lad os oprette en model, der bruger et undersæt af funktioner i vores dataset. Du kan vende tilbage og vælge forskellige funktioner, køre forsøget igen og se, hvis du får bedre resultater. Men hvis du vil starte, skal du prøve følgende funktioner:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Træk en anden [Vælg kolonner i Dataset] [ select-columns] modul til forsøget lærredet, og Tilslut den til venstre outputporten [Ren manglende Data] [ clean-missing-data] modul. Dobbeltklik på modulet, og skriv "Vælg funktioner til forudsigelse."

2. Klik på **Start kolonnevælger** i ruden **Egenskaber** .

3. Klik på **regler**.

4. Klik på **Ingen kolonner**under **Begynde med** , og derefter vælge **Medtag** og **kolonnenavne** i rækken filter. Angiv vores liste over kolonnenavne. Dette bestemmer, at modulet skal kunne passere gennem kun kolonner, vi angiver.

    > [AZURE.TIP] Kører forsøget, har vi sikret, at kolonnedefinitioner til vores data passerer fra datasættet [Ren manglende Data] [ clean-missing-data] modul. Det betyder, at andre moduler, du opretter forbindelse får også oplysninger fra datasættet.

5. Klik på knappen markeret (OK).

![Vælg kolonner][screen6]

Dette giver det datasæt, der skal bruges i algoritmen undervisning i de næste trin. Du kan senere vende tilbage og prøve igen med et andet valg af funktioner.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Trin 4: Vælge og anvende en learning-algoritme

Nu, hvor dataene er klar, består at konstruere en forudseende model af træning, og test. Vi bruger vores data at instruere modellen og derefter teste model for at se, hvor tæt den er i stand til at forudsige priser. Nu skal ikke bekymre dig om, hvorfor vi har brug for at træne og test derefter en model.

*Klassificering* og *regression* er to typer af kontrollerede machine learning-teknikker. Klassificering forudsiger et svar fra et defineret sæt af kategorier, som en farve (rød, blå eller grøn). Regression, bruges til at forudsige et tal.

Da vi ønsker at forudsige pris, som er et tal, bruger vi en regressionsmodel. Vi skal træne en simpel *lineær regression* model for eksempel, og i det næste trin, vil vi teste den.

1. Vi bruger vores data for undervisning og test ved at opdele det i separate undervisning og test sæt. Vælg og træk de [Opdelte Data] [ split] modul til forsøget lærredet, og Tilslut den til output af sidste [Vælg kolonner i Dataset] [ select-columns] modul. Angiv **brøkdel af rækker i den første afgang dataset** til 0,75. På denne måde kan vi bruge 75 procent af data til at uddanne modellen, og hold tilbage 25 procent til test.

    > [AZURE.TIP] Ved at ændre parameteren **tilfældig frø** , kan du oprette forskellige stikprøver til uddannelse og prøvning. Denne parameter angiver forhåndsudfyldning af den pseudo-tilfældige tal generator.

2. Kør forsøget. Dette giver mulighed for at [Vælge kolonner i Dataset] [ select-columns] og [Opdelte Data] [ split] moduler til at overføre kolonnedefinitioner til modulerne vi tilføjer hele tiden ud.  

3. Vælg algoritmen learning, udvide kategorien **Machine Learning** i paletten modul til venstre for lærredet, og udvid derefter **Initialisere Model**. Dette viser flere kategorier af moduler, der kan bruges til at initialisere machine learning algoritmer.

    Vælg den [Lineære Regression] for dette eksperiment,[ linear-regression] modulet under kategorien **Regression** (du kan også finde modulet ved at skrive "lineær regression" i søgefeltet palet), og træk det til lærredet eksperiment.

4. Find og træk [Tog Model] [ train-model] modul til lærredet eksperiment. Tilsluttes outputtet for den [Lineære Regression] af venstre inputporten[ linear-regression] modul. Højre inputporten tilsluttes uddannelse uddata (venstre port) af de [Opdelte Data] [ split] modul.

5. Vælg [Model toget] [ train-model] modul, skal du klikke på **Start kolonnevælger** i ruden **Egenskaber** , og vælg derefter kolonnen **pris** . Dette er den værdi, der skal forudsige vores model.

    ![Marker kolonne "pris"][screen7]

6. Kør forsøget.

Resultatet er en uddannet regressionsmodel, der kan bruges til at give nye prøver for at komme med forudsigelser.

![Anvendelse af machine learning-algoritme][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>Trin 5: Forudsige nye biler priser

Vi har uddannet modellen med 75 procent af vores data, vi kan bruge den til at give de andre 25% af dataene for at se, hvor godt funktioner vores model.

1. Find og træk [Score Model] [ score-model] modul til forsøget lærred og Tilslut venstre inputporten til output af [Toget Model] [ train-model] modul. Højre inputporten tilsluttes test data output (højre port) af de [Opdelte Data] [ split] modul.  

    ![Score Model modul][screen8a]

2. At køre forsøget, og få vist output fra [Score Model] [ score-model] modul, klik på outputport, og vælg derefter **visuelle effekter**. Output vises de forudberegnede værdier for pris og de kendte værdier fra testdataene.  

3. Endelig, for at teste kvaliteten af resultaterne, markere og trække [Evaluere Model] [ evaluate-model] modul til forsøget lærredet, og forbind venstre inputporten til output af [Score Model] [ score-model] modul. (Der er to porte på input, da [Vurdere Model] [ evaluate-model] modul kan bruges til at sammenligne to modeller.)

4. Kør forsøget.

Til at få vist output fra [Evaluere Model] [ evaluate-model] modul, klik på outputport, og vælg derefter **visuelle effekter**. Der vises følgende statistikker for vores model:

- **Betyder absolut fejl** (MAE): gennemsnitlige absolutte fejl (en *fejl* er forskellen mellem den forventede og faktiske værdi).
- **Roden gennemsnitlige kvadrerede fejl** (RMSE): kvadratroden af gennemsnittet af kvadrerede fejl af forudsigelser på test-datasæt.
- **Relative absolutte fejl**: gennemsnitlige absolutte fejl i forhold til den absolutte forskel mellem faktiske værdier og gennemsnitsværdien for alle faktiske værdier.
- **Relative kvadrerede fejl**: gennemsnittet af kvadrerede fejl i forhold til den kvadrerede forskel mellem de faktiske værdier og gennemsnitsværdien for alle faktiske værdier.
- **Koefficient af bestemmelsen**: også kendt som **R kvadrerede værdi**, dette er en statistisk metrikværdi, der angiver, hvor godt en model passer til dataene.

Statistik, mindre er bedre for hver af fejlen. En mindre værdi angiver, at forudsigelser standarddokumentskabelonerne, så de faktiske værdier. Til **Koefficient af bestemmelse**, jo tættere dens værdi er en (1.0), jo bedre forudsigelser.

![Evalueringsresultater][screen9]

Endelig forsøget bør se sådan ud:

![Machine learning Selvstudium: udføre lineær regression eksperiment, der bruger forudseende modellering teknikker.][screen10]

## <a name="next-steps"></a>Næste trin

Nu, hvor du har fuldført et første machine learning selvstudium og har dit eksperiment, der er oprettet, kan du navigere for at forsøge at forbedre modellen. For eksempel kan du ændre de funktioner, du bruger i din forudsigelse. Eller du kan redigere egenskaberne for den [Lineære Regression] [ linear-regression] algoritme, eller prøv en anden algoritme helt. Du kan selv tilføje flere machine learning algoritmer til dit eksperiment på én gang og sammenligne to ved hjælp af [Evaluere Model] [ evaluate-model] modul.

> [AZURE.TIP] Brug knappen **Gem som** under forsøget lærredet til at kopiere en gentagelse af dit eksperiment. Du kan se alle gentagelser af dit eksperiment ved at klikke på **Vis oversigt over køre** under på lærredet. Se [Administrer eksperimentere gentagelser i Azure Machine Learning Studio] [ runhistory] for at få yderligere oplysninger.

[runhistory]: machine-learning-manage-experiment-iterations.md

Når du er tilfreds med din model, kan du installere den som en webtjeneste, der skal bruges til at forudsige bil priser ved hjælp af nye data. Under [installation en webtjeneste Azure Machine Learning] [ publish] for at få yderligere oplysninger.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

En mere omfattende og detaljeret gennemgang af forudseende modellering teknikker til oprettelse af uddannelse, vundne og implementering af en model, se [udarbejde en forudseende løsning ved hjælp af Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
