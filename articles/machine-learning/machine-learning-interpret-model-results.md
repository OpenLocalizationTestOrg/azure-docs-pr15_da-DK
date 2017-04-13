<properties
    pageTitle="Fortolke model resultater i Machine Learning | Microsoft Azure"
    description="Sådan vælger du den optimal parameter, der er angivet for en algoritme bruger og visualisere karakteren model output."
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


# <a name="interpret-model-results-in-azure-machine-learning"></a>Fortolke model resultater i Azure Machine Learning

Dette emne forklares, hvordan at visualisere og fortolke forudsigelse resultater i Azure Machine Learning Studio. Når du har uddannelse en model og færdig prognoser ovenpå ("karakter modellen"), skal du forstå og fortolke forudsigelse resultatet.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Der er fire overordnede typer machine learning-modeller i Azure maskine undervisning:

* Klassifikation
* Klynge
* Simple lineære regression
* Anbefalet af systemer

De moduler, der bruges til forudsigelse oven på disse modeller er:

* [Resultat Model] [ score-model] -modul til klassificering og regression
* [Tildele til klynger] [ assign-to-clusters] -modul til klynge
* [Resultat anbefalet af Matchbox] [ score-matchbox-recommender] til anbefaling systemer

Dette dokument forklarer, hvordan du fortolker forudsigelse resultater for hver af disse moduler. Se, [hvordan du vælger parametre for at optimere dine algoritmer i Azure Machine Learning](machine-learning-algorithm-parameters-optimize.md)for en oversigt over disse moduler.

I dette emne beskrives forudsigelse fortolkning, men ikke beregning i modellen. Du kan finde flere oplysninger om, hvordan du evaluere din model, se, [hvordan du evaluere model ydeevne i Azure Machine Learning](machine-learning-evaluate-model-performance.md).

Hvis du er ny bruger af Azure Machine Learning og har brug for hjælp til at oprette en simpel forsøg at komme i gang skal du se [oprette en simpel forsøg i Azure Machine Learning Studio](machine-learning-create-experiment.md) i Azure Machine Learning Studio.

## <a name="classification"></a>Klassifikation ##
Der er to underkategorier klassifikation problemerne:

* Problemer med kun to klasser (to klasse eller binære klassifikation)
* Problemer med mere end to klasser (med flere klasse klassifikation)

Azure Machine Learning har forskellige moduler til at behandle hver af disse typer klassifikation, men metoder til at fortolke feltresultater tekstfuldførelse ligner hinanden.

### <a name="two-class-classification"></a>To-klasse klassifikation###
**Eksempel forsøg**

Et eksempel på et problem med to klasse klassifikation er klassificeringen af iris blomster. Opgaven er til at klassificere iris blomster baseret på deres funktioner. Datasættet Iris angivet i Azure Machine Learning er et undersæt af de populære [Iris datasæt](http://en.wikipedia.org/wiki/Iris_flower_data_set) , der indeholder forekomster af kun to blomst art (klasser 0 og 1). Der findes fire funktioner for hver blomst (sepal længde, sepal bredde, bladet længde og bladet bredde).

![Skærmbillede af iris forsøg](./media/machine-learning-interpret-model-results/1.png)

Figur 1. IRIS to klasse klassifikation problem forsøg

Et forsøg er udført for at løse problemet, som vist i figur 1. En to-klasse øget beslutning træet model har uddannelse og karakter. Nu kan du visualisere forudsigelse resultaterne fra [Karakteren Model] [ score-model] modul ved at klikke på outputporten [Karakteren Model] [ score-model] modul og derefter klikke på **visuelle effekter**.

![Resultat model modul](./media/machine-learning-interpret-model-results/1_1.png)

Dette viser vurdering resultaterne som vist i figur 2.

![Resultater af iris to klasse klassifikation forsøg](./media/machine-learning-interpret-model-results/2.png)

Figur 2. Visualisere et resultat model resultat i to klasse klassifikation

**Resultatet fortolkning**

Der er seks kolonner i Resultattabellen. Venstre fire kolonner er de fire funktioner. De højre to kolonner, karakter etiketter og karakter sandsynligheder er forudsigelse resultater. Kolonnen karakter sandsynligheder viser sandsynligheden, som en blomst tilhører klassen positive (klasse 1). Det første tal i den kolonne (0.028571) betyder, at der er for eksempel 0.028571 sandsynligheden for, at den første blomst tilhører klasse 1. Kolonnen karakter etiketter viser den estimerede klasse for hver blomst. Dette er baseret på kolonnen karakter sandsynligheder. Hvis scored sandsynligheden for et blomst er større end 0,5, er den forudsat som klasse 1. Ellers er den forudsat som klasse 0.

**Tjenesten webpublikation**

Hvis resultaterne forudsigelse har forstået og vurderet lyd, kan forsøget publiceres som en webtjeneste, så du kan installere det i forskellige programmer, og kald den for at hente klasse prognoser på en hvilken som helst nye iris blomst. For at lære at ændre et kursus forsøg til en vurdering forsøg og publicere den som en webtjeneste, skal du se [Publicer webtjenesten Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md). Denne procedure giver dig en vurdering forsøg, som vist i figur 3.

![Skærmbillede af vundne forsøg](./media/machine-learning-interpret-model-results/3.png)

Figur 3. Vundne iris to klasse klassifikation problem forsøg

Nu skal du angive input og output til webtjenesten. Input er den rigtige input port af [Karakteren Model][score-model], som er Iris blomst funktioner input. Valg af output, afhænger af, om du er interesseret i klassen estimerede (karakter for etiketten), scored sandsynlighed eller begge dele. I dette eksempel antages det, at du er interesseret i begge dele. For at vælge de ønskede outputkolonner skal du bruge [Vælg kolonner i datasæt] [ select-columns] modul. Klik på [Vælg kolonner i datasæt][select-columns], skal du klikke på **Start kolonne Datavælger**, og vælg **Karakter etiketter** og **Karakter sandsynligheder**. Når du har indstillet outputport af [Vælg kolonner i datasæt] [ select-columns] og køre den igen, skal du være klar til at publicere vurdering forsøget som en webtjeneste ved at klikke på **PUBLICER WEBTJENESTE**. Endelige forsøget ser ud som figur 4.

![Iris to klasse klassifikation forsøget](./media/machine-learning-interpret-model-results/4.png)

Figur 4. Endelige resultat forsøg en iris to klasse klassifikation problemet

Når du kører webtjenesten og indtaste nogle funktion værdier i en forekomst af test, returnerer resultatet to tal. Det første tal er scored etiketten, og andet er scored sandsynligheden. Denne blomst er skønsmæssigt beregnet som klasse 1 med 0.9655 sandsynlighed.

![Test fortolkning karakteren model](./media/machine-learning-interpret-model-results/4_1.png)

![Vundne testresultater](./media/machine-learning-interpret-model-results/5.png)

Figur 5. Web service resultatet af iris to klasse klassifikation

### <a name="multi-class-classification"></a>Flere klasse klassifikation
**Eksempel forsøg**

I dette forsøg udfører du en brev-anerkendelse opgave som et eksempel på multiclass klassificering. Klassificeringen forsøger at forudsige et bestemt bogstav (klasse) baseret på nogle håndskrevne attributværdier, der er hentet fra de håndskrevne billeder.

![Bogstav talegenkendelse eksempel](./media/machine-learning-interpret-model-results/5_1.png)

I kursus data, der er 16 funktioner, der er hentet fra håndskrevne bogstav billeder. 26 bogstaverne formular vores 26 klasser. Figur 6 viser et forsøg, der skal oplære en multiclass klassifikation model for bogstav talegenkendelse og forudsige på den samme funktion, der er angivet på datasættet test.

![Bogstav talegenkendelse multiclass klassifikation forsøg](./media/machine-learning-interpret-model-results/6.png)

Figur 6. Bogstav talegenkendelse multiclass klassifikation problem forsøg

Visualisere resultaterne fra [Karakteren Model] [ score-model] modul ved at klikke på outputporten af [Karakteren Model] [ score-model] modul og derefter klikke på **visuelle effekter**, skal du se indhold, som vist i figur 7.

![Resultat model resultater](./media/machine-learning-interpret-model-results/7.png)

Figur 7. Visualisere karakteren model resultaterne i en med flere klasse klassifikation

**Resultatet fortolkning**

Venstre 16 kolonnerne repræsenterer funktion værdierne i sættet test. Kolonner med navne ud karakter sandsynligheder klassen "XX" er lige som kolonnen karakter sandsynligheder i to klasse store og små bogstaver. De vises sandsynligheden, der falder den tilsvarende post i en bestemt kategori. Der er for eksempel 0.003571 sandsynligheden for, at det er en "A" 0.000451 sandsynligheden for, at det er en "B", og så videre til den første post. Den sidste kolonne (karakter for etiketter) er den samme som karakter etiketter i to klasse store og små bogstaver. Markerer klassen med den største scored sandsynlighed som den tilsvarende post estimerede klasse. For eksempel for den første post scored etiketten er "F" da den har den største sandsynlighed skal være en "F" (0.916995).

**Tjenesten webpublikation**

Du kan også finde det scored navn for hver post og sandsynligheden for etiketten scored. Grundlæggende logik er at finde den største sandsynlighed blandt alle scored sandsynligheder. For at gøre dette, skal du bruge [Udføre R Script] [ execute-r-script] modul. R-kode er vist i figur 8, og resultatet af forsøget vises i figur 9.

![Eksempel på R kode](./media/machine-learning-interpret-model-results/8.png)

Figur 8. R-kode til at udtrække karakter etiketter og de tilknyttede sandsynligheder af etiketterne

![Forsøg resultat](./media/machine-learning-interpret-model-results/9.png)

Figur 9. Endelige resultat forsøg problemets bogstav talegenkendelse multiclass klassifikation

Når du publicerer og køre webtjenesten og angive nogle input funktion værdier, de returnerede resultat ser ud, som figur 10. Denne håndskrevne bogstav med dens udpakkede 16 funktioner er skønsmæssigt beregnet skal være en "T" med 0.9715 sandsynlighed.

![Test fortolkning karakteren modul](./media/machine-learning-interpret-model-results/9_1.png)

![Testresultat](./media/machine-learning-interpret-model-results/10.png)

Figur 10. Web service resultatet af multiclass klassifikation

## <a name="regression"></a>Simple lineære regression

Problemer med at regression er forskellige fra klassifikation problemer. I et klassifikation problem forsøger du at forudsige dedikeret klasser, som som klasse en iris blomst tilhører. Men som du kan se i følgende eksempel på et problem med regression, du forsøger at forudsige en fortløbende variabel, som en bil pris.

**Eksempel forsøg**

Bruge bil pris forudsigelse som din eksempel for regression. Du forsøger at forudsige en bil, der er baseret på dets funktioner, herunder foretage, brændselstype, brødtekst type og drev hjulet pris. Forsøget er vist i figur 11.

![Bil pris regression forsøg](./media/machine-learning-interpret-model-results/11.png)

Figur 11. Bil pris regression problem forsøg

Visualisere [Karakteren Model] [ score-model] modulet resultatet ser sådan figur 12.

![Vundne resultater til bil pris forudsigelse problem](./media/machine-learning-interpret-model-results/12.png)

Figur 12. Vundne resultat for bil pris forudsigelse problemet

**Resultatet fortolkning**

Karakter for etiketter er kolonnen resultatet i dette resultat resultat. Tallene er den estimerede pris for hver bil.

**Tjenesten webpublikation**

Du kan udgive regression forsøget til en webtjeneste og kald den til bil pris forudsigelse på samme måde som i to klasse klassifikation use case.

![Vundne forsøg til bil pris regression problem](./media/machine-learning-interpret-model-results/13.png)

Figur 13. Vundne forsøg en bil pris regression problemet

Kører webtjenesten, det returnerede resultat ser ud som figur 14. Den estimerede pris for denne bil er $15,085.52.

![Test fortolkning vurdering modul](./media/machine-learning-interpret-model-results/13_1.png)

![Vundne modul resultater](./media/machine-learning-interpret-model-results/14.png)

Figur 14. Web service resultatet af en bil pris regression problem

## <a name="clustering"></a>Klynge

**Eksempel forsøg**

Lad os bruge datasættet Iris igen til at oprette en klyngedannelse forsøg. Her kan du filtrere ud klasse etiketterne i datasættet så den kun indeholder funktioner og kan bruges til klynge. I denne iris brugseksempel skal du angive antallet klynger skal være to under processen kursus, hvilket betyder, at du vil samle blomster i to klasser. Forsøget er vist i figur 15.

![IRIS klyngedannelse problem eksperimentere](./media/machine-learning-interpret-model-results/15.png)

Figur 15. IRIS klyngedannelse problem eksperimentere

Klynge er forskellig fra klassifikation, i den valgte datasættet kursus ikke har bunden sandheden etiketter alene. Klynge grupper kursus datasæt forekomster i særskilte klynger. Under processen kursus etiketter modellen posterne ved at lære forskellene mellem deres funktioner. Herefter kan erfaren modellen bruges til at klassificere yderligere fremtidige poster. Der findes to dele af resultatet er interesseret i at inden for et klyngedannelse problem. Den første del er etiketter datasættet kursus, og andet klassificerer et nyt datasæt med erfaren modellen.

Den første del af resultatet kan visualiseres ved at klikke på den venstre outputport af [Tog klynge Model] [ train-clustering-model] og derefter klikke på **visuelle effekter**. Visualiseringen vises i figur 16.

![Klynge resultat](./media/machine-learning-interpret-model-results/16.png)

Figur 16. Visualisere klynge resultat for datasættet kursus

Resultatet af den anden del klynge nye poster med erfaren klyngedannelse modellen, vises i figur 17.

![Visualisere klynge resultat](./media/machine-learning-interpret-model-results/17.png)

Figur 17. Visualisere klynge resultat på et nyt datasæt

**Resultatet fortolkning**

Selvom resultatet af de to dele stammer fra forskellige forsøg faser, ser ens og fortolkes på samme måde. De første fire kolonner er funktioner. Den sidste kolonne, opgaver, er resultatet forudsigelse. De poster, der er tildelt den samme tal forventes skal være i samme klynge, det vil sige, de dele ligheder mellem anden måde (dette forsøg bruger standard Euclidean afstand metrisk). Fordi du har angivet antallet af klynger skal være 2, mærket tildelinger elementerne er 0 eller 1.

**Tjenesten webpublikation**

Du kan udgive klyngedannelse forsøget til en webtjeneste og kald den til klynge prognoser på samme måde som i to klasse klassifikation brug af store og små bogstaver.

![Vundne forsøg til iris klyngedannelse problem](./media/machine-learning-interpret-model-results/18.png)

Figur 18. Vundne forsøg en iris klyngedannelse problemet

Når du kører webtjenesten, ligner det returnerede resultat figur 19. Denne blomst er skønsmæssigt beregnet skal være i klynge 0.

![Test fortolke vurdering modul](./media/machine-learning-interpret-model-results/18_1.png)

![Vundne modul resultat](./media/machine-learning-interpret-model-results/19.png)

Figur 19. Web service resultatet af iris to klasse klassifikation

## <a name="recommender-system"></a>Anbefalet af system
**Eksempel forsøg**

Du kan bruge restaurant anbefaling problemet for anbefalet af systemer, som et eksempel: Du kan anbefale restauranter for kunder, der er baseret på deres bedømmelse historie. Inputdataene består af tre dele:

* Restaurant bedømmelser fra kunder
* Funktionen kundedata
* Restaurant funktion data

Der er flere ting, som vi kan gøre med [Tog Matchbox anbefalet af] [ train-matchbox-recommender] modul i Azure maskine undervisning:

- Forudsige bedømmelser for en given bruger og element
- Anbefale elementer til en given bruger
- Søg efter brugere, der er relateret til en given bruger
- Finde elementer, der er relateret til et bestemt element

Du kan vælge, hvad du vil gøre ved at vælge mellem de fire indstillinger i menuen **Anbefalet af forudsigelse type** . Her kan du gennemgå alle fire scenarier.

![Anbefalet af matchbox](./media/machine-learning-interpret-model-results/19_1.png)

En typisk Azure Machine Learning forsøg for et anbefalet af system ser ud som figur 20. Oplysninger om, hvordan du bruger disse anbefalet af systemmoduler finder du [tog matchbox anbefalet af] [ train-matchbox-recommender] og [karakteren matchbox anbefalet af][score-matchbox-recommender].

![Anbefalet af system forsøg](./media/machine-learning-interpret-model-results/20.png)

Figur 20. Anbefalet af system forsøg

**Resultatet fortolkning**

**Forudsige bedømmelser for en given bruger og element**

Ved at vælge **Bedømmelse forudsigelse** under **Anbefalet af forudsigelse type**, stiller du anbefalet af systemet til at forudsige bedømmelse for en given bruger og element. Visualisering af [Resultatet Matchbox anbefalet af] [ score-matchbox-recommender] output ligner figur 21.

![Give resultat af anbefalet af systemet – vurdering forudsigelse](./media/machine-learning-interpret-model-results/21.png)

Figur 21. Visualisere karakteren resultatet af anbefalet af systemet – vurdering forudsigelse

De første to kolonner er par bruger elementer fra inputdataene. Den tredje kolonne er den estimerede klassifikation for en bruger til et bestemt element. For eksempel i den første række kunde U1048 er skønsmæssigt beregnet til rente restaurant 135026 som 2.

**Anbefale elementer til en given bruger**

Ved at vælge **Element anbefaling** under **Anbefalet af forudsigelse type**, beder du anbefalet af systemet anbefale elementer til en given bruger. Den sidste parameter til at vælge i dette scenarie er *anbefalede element markering*. Indstillingen **Fra bedømt elementer (for model evaluering)** er primært til model evaluering under processen kursus. Denne forudsigelse fase vælge vi **Fra alle elementer**. Visualisering af [Resultatet Matchbox anbefalet af] [ score-matchbox-recommender] output ligner figur 22.

![Give resultat af anbefalet af system - element anbefaling](./media/machine-learning-interpret-model-results/22.png)

Figur 22. Visualisere karakteren resultatet af anbefalet af systemet – element anbefaling

Først af seks kolonner repræsenterer den angivne bruger-id'er til anbefaler elementer efter, som leveres af inputdataene. De fem kolonner repræsenterer de elementer, der anbefales til brugeren i faldende rækkefølge efter relevans. For eksempel i den første række er mest anbefalede restauranten for kunde U1048 134986, efterfulgt af 135018, 134975, 135021 og 132862.

**Søg efter brugere, der er relateret til en given bruger**

Ved at vælge **Relaterede brugere** under **Anbefalet af forudsigelse type**, beder du anbefalet af systemet til at finde relaterede brugere til en bestemt bruger. Relaterede brugere er de brugere, der har samme indstillinger. Den sidste parameter til at vælge i dette scenarie er *Relaterede bruger markering*. Indstillingen **Fra brugere, der er klassificeret elementer (for model evaluering)** er primært til model evaluering under processen kursus. Vælge **Fra alle brugere** for stadium forudsigelse. Visualisering af [Resultatet Matchbox anbefalet af] [ score-matchbox-recommender] output ligner figur 23.

![Give resultat af anbefalet af system - relaterede brugere](./media/machine-learning-interpret-model-results/23.png)

Figur 23. Visualisere karakteren resultaterne af anbefalet af systemet – relaterede brugere

Først af seks kolonner viser den angivne bruger id'er behov for at finde relaterede brugere, som leveres af indtastede data. De fem kolonner gemme de estimerede relaterede brugere af brugeren i faldende rækkefølge efter relevans. For eksempel i den første række er mest relevante kunden for kunde U1048 U1051, efterfulgt af U1066, U1044, U1017 og U1072.

**Finde elementer, der er relateret til et bestemt element**

Ved at markere **Relaterede elementer** under **Anbefalet af forudsigelse type**, stiller du anbefalet af systemet til at finde relaterede elementer til et bestemt element. Relaterede elementer er de mest sandsynlige til at være synes godt om af den samme bruger elementer. Den sidste parameter til at vælge i dette scenarie er *Relaterede element markering*. Indstillingen **Fra bedømt elementer (for model evaluering)** er primært til model evaluering under processen kursus. Vi vælge **Fra alle elementer** for denne forudsigelse fase. Visualisering af [Resultatet Matchbox anbefalet af] [ score-matchbox-recommender] output ligner figur 24.

![Give resultat af anbefalet af system - relaterede elementer](./media/machine-learning-interpret-model-results/24.png)

Figur 24. Visualisere karakteren resultaterne af anbefalet af systemet – relaterede elementer

Først af seks kolonner repræsenterer det angivne element id'er, der er behov for at finde relaterede elementer, som leveres af inputdataene. De fem kolonner gemme de estimerede relaterede elementer af elementet i faldende rækkefølge med hensyn til relevans. I den første række er mest relevante elementet efter element 135026 for eksempel 135074, efterfulgt af 135035, 132875, 135055 og 134992.

**Tjenesten webpublikation**

Processen med at udgive disse forsøg som webtjenester at få prognoser minder for hvert af de fire scenarier. Her kan vi tager det andet scenario (anbefalet elementer til en bestemt bruger) som et eksempel. Du kan følge den samme procedure med de andre tre.

Gemme erfaren anbefalet af systemet som en erfaren model og filtrering inputdataene til en enkelt bruger-ID-kolonne, som ønsket, kan du logge på forsøget som figur 25 og publicere den som en webtjeneste.

![Vundne forsøg til restaurant anbefaling problemet](./media/machine-learning-interpret-model-results/25.png)

Figur 25. Vundne forsøg til restaurant anbefaling problemet

Kører webtjenesten, det returnerede resultat ser ud som figur 26. De fem anbefalede restauranter for bruger U1048 er 134986, 135018, 134975, 135021 og 132862.

![Eksempel på anbefalet af systemtjenesten](./media/machine-learning-interpret-model-results/25_1.png)

![Eksempel på forsøg resultater](./media/machine-learning-interpret-model-results/26.png)

Figur 26. Web service resultatet af restaurant anbefaling problem


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
