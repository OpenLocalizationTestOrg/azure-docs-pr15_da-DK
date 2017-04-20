<properties
    pageTitle="Vælg parametre for at optimere din algoritmer i Azure Machine Learning | Microsoft Azure"
    description="Forklarer, hvordan du vælger den optimale parameter, der er angivet for en algoritme i Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />


# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Vælg parametre for at optimere din algoritmer i Azure Machine Learning

Dette emne beskriver, hvordan du vælger den højre hyperparameter, der er angivet for en algoritme i Azure Machine Learning. De fleste machine learning algoritmer har parametre til opsætning. Når du træner en model, skal du angive værdier for disse parametre. Effektiviteten af den model, der er uddannet afhænger af parametrene model, du vælger. Processen med at finde den optimale sæt parametre er kendt som *valg af model*.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Der er forskellige måder at model valg. Cross-validering er en af de mest udbredte metoder til valg af model i machine learning, og det er standard model valg mekanismen i Azure Machine Learning. Da Azure Machine Learning understøtter både R og Python, kan du altid implementerer deres egne mekanismer for udvælgelse af model ved hjælp af enten R eller Python.

Der er fire trin ved at finde det bedste parametersæt:

1.  **Definer parameter pladsen**: algoritme først beslutte nøjagtige parameterværdierne skal overveje.
2.  **Definer cross-valideringen indstillinger**: beslutte, hvordan du vælger tværs validering folder for datasættet.
3.  **Definer metrikværdien**: beslutte hvilken metrikværdien skal bruge til at bestemme det bedste sæt af parametre, som nøjagtighed, roden middelværdi kvadrerede fejl, precision, tilbagekaldelse eller f-score.
4.  **Tog, vurdere og sammenligne**: For hver enkelt kombination af parameterværdierne, tværgående validering udføres af og baseret på den fejl metrikværdi, du definerer. Efter evaluering og sammenligning, kan du vælge den bedst ydende model.

Følgende billede viser viser, hvordan dette kan opnås i Azure Machine Learning.

![Find det bedste parametersæt](./media/machine-learning-algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definere parameteren-plads
Du kan definere parameteren til trinnet model initialisering. Ruden parameter i alle machine learning algoritmer har to tilstande til underviser: *Enkelt Parameter* og *Parameteren rækkevidde*. Vælg parameterområde tilstand. Du kan angive flere værdier for hver parameter i parameterområde tilstand. Du kan angive kommaseparerede værdier i tekstboksen.

![To klasse forstærket beslutningstræ, enkelt parameter](./media/machine-learning-algorithm-parameters-optimize/fig2.png)

 Alternativt kan du definere de største og mindste punkter i gitteret og det samlede antal point for at blive genereret med **Brug område Builder**. Som standard oprettes parameterværdierne i en lineær skala. Men hvis **Logaritmisk skala** er markeret, oprettes værdierne i logaritmisk skala (forholdet mellem de tilstødende punkter er konstant i stedet for deres forskel). Du kan definere et område for heltal parametre ved hjælp af en bindestreg. For eksempel "1-10" betyder, at alle heltal mellem 1 og 10 (begge inklusive) udgør parametersæt. Blandet tilstand understøttes også. For eksempel angive parameteren "1-10, 20, 50" ville omfatte heltal 1-10, 20 og 50.

![To klasse forstærket beslutningstræ, parameterområde](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definere validering af tværgående foldelinjer
[Partition og prøve] [ partition-and-sample] modul kan bruges til at tildele tilfældigt folder til dataene. I følgende eksempel konfiguration for modulet, vi definerer fem Folder og nummerere tilfældigt foldning eksempel-forekomster.

![Partition og prøve](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## <a name="define-the-metric"></a>Definer målestokken
[Tune Model Hyperparameters] [ tune-model-hyperparameters] modul understøtter empirisk at vælge det bedste sæt af parametre for en bestemt algoritme og dataset. Ud over andre oplysninger indeholder angående undervisning model, ruden **Egenskaber** i dette modul metrikværdien for at bestemme den bedste parametersæt. Den har to forskellige rullelisten bokse for klassificering og regression-algoritmer, henholdsvis. Regression metrikværdien ignoreres, hvis algoritmen, der overvejes en klassificering algoritme, og omvendt. I dette specifikke eksempel er metrikværdien **nøjagtighed**.   

![Sweep parametre](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Træne, evaluere og sammenligne  
Samme [Stil Model Hyperparameters] [ tune-model-hyperparameters] modul træner alle de modeller, der svarer til parametersæt, evaluerer forskellige former for statistik og opretter derefter den bedst uddannede model, der er baseret på den metrikværdi, du vælger. Dette modul indeholder to obligatoriske input:

* Deltageren baseret
* Datasættet

Modulet har også en valgfri dataset input. Forbind datasættet med foldning oplysninger til det obligatoriske datasæt input. Hvis datasættet ikke er tildelt alle oplysninger, foldning, udføres en 10-fold tværgående validering automatisk som standard. Hvis foldning tildelingen sker ikke, og en validering af et datasæt leveres i valgfri dataset havn, tog testtilstand er valgt, og det første datasæt, der bruges til at træne modellen for hver kombination af parametre.

![Forstærket beslutning træ klassificering](./media/machine-learning-algorithm-parameters-optimize/fig6a.png)

Derefter evalueres modellen på validering af datasættet. Venstre outputporten modul, der viser forskellige måleenheder som funktion af parameterværdier. Højre outputporten giver den uddannede model, der svarer til den bedst ydende modellen i henhold til den valgte metrik (**nøjagtighed** i dette tilfælde).  

![Validering af datasæt](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)

Du kan se valgt af visualisere højre outputporten nøjagtige parametre. Denne model kan bruges i et sæt test for vundne eller en webtjeneste, der er opgivet efter lagring som en uddannet model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
