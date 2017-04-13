<properties
   pageTitle="Forudsige et svar med en enkelt model - regressionsmodel | Microsoft Azure"
   description="Sådan oprettes en simpel regressionsmodel for at forudsige en pris i Data videnskabelige for begyndere video 4. Indeholder en lineær regression med target data."                                  
   keywords="oprette en model, enkelt model, pris forudsigelse, simpel regressionsmodel"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="predict-an-answer-with-a-simple-model"></a>Forudsige et svar med en enkelt model

## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Data videnskabelige for begyndere serie

Lær at oprette en simpel regressionsmodel for at forudsige prisen på en rombe i Data videnskabelige for begyndere video 4. Vi vil tegne en regressionsmodel med target data.

For at få mest muligt ud af serien skal du se dem alle. [Gå til listen over videoerne](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-predict-an-answer-with-a-simple-model]

## <a name="other-videos-in-this-series"></a>Andre videoer i denne serie

*Data videnskabelige for begyndere* er en hurtig introduktion til data videnskab i fem korte videoer.

  * Video 1: [5 spørgsmål data videnskabelige svar](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
  * Video 2: [er dine data er du klar til data videnskabelige?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
  * Video 3: [Stil et spørgsmål, kan du besvare med data](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek.)*
  * Video 4: Forudsige et svar med en enkelt model
  * Video 5: [Kopiere andres arbejde for at gøre data videnskabelige](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Afskrift: Forudsige et svar med en enkelt model

Velkommen til fjerde videoen i den "Data videnskabelige for begyndere" serie. I denne tastaturgenvej, skal vi oprette en simpel model og foretage en forudsigelse.

En *model* er en forenklet tekstenhed om vores data. Jeg viser dig, hvad jeg betyder.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Indsaml relevante, nøjagtige, forbundne, nok data

Jeg vil butik for en rombe, at. Jeg har en ringetone, min bedstemor med en indstilling for en 1.35 karat rombe-id'er, og jeg vil have en ide om, hvor meget det vil koste. Jeg tager en Notesblok og pen på butikken, objekter, og jeg Notér pris alle ruder i tilfælde, og hvor meget de afveje i carats. Starte med den første rombe - det er 1.01 carats og $7,366.

Nu jeg gennemfører og gøre dette for alle andre ruder i store.

![Datakolonner rombe](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Bemærk, at vores liste har to kolonner. Hver kolonne har en anden attribut - vægt i carats og pris- og hver række er et enkelt datapunkt, der repræsenterer en enkelt rombe.

Vi har faktisk oprettet en lille data Angiv her - en tabel. Bemærk, at den opfylder vores kriterier for kvalitet:

* Data, der er **relevante** - vægt er helt relateret til pris
* Det er **præcis** – vi double-checked de priser, som vi Notér
* Det er **tilsluttet** – er ikke nogen tomme mellemrum i en af disse kolonner
* Og, som vi ser, er **ikke nok** data til at besvare vores spørgsmål

## <a name="ask-a-sharp-question"></a>Stil et skarpe spørgsmål

Nu vi kan dog udgøre vores spørgsmål i en skarpe metode: "hvor meget koster det til at købe en 1.35 karat rombe?"

Vores liste har ikke en 1.35 karat rombe i den, så vi har til at bruge resten af vores data for at få et svar på spørgsmålet.

## <a name="plot-the-existing-data"></a>Afbilde de eksisterende data

Vi bruger ikke tegne en vandret streg, tal, der kaldes en akse, hvis du vil oprette et diagram på tykkelser. Området af vægt er 0-2, så vi kan tegne en streg, der dækker, interval og placere akser for hver halve karat.

Næste skal vi tegn en lodret akse for at optage prisen og forbinde den til den vandrette vægt akse. Dette vil være i enheder af kroner. Vi har nu et sæt koordinere akser.

![Vægt og pris akser](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vi til at tage disse data nu og ændre aftalen til et *punkt afbilde*. Dette er en god måde at visualisere numeriske datasæt.

For det første datapunkt overskue vi en lodret streg på 1.01 carats. Vi overskue derefter en vandret streg på $7,366. Hvis de opfylder, tegne vi en prik. Dette repræsenterer vores første rombe.

Nu vi gå gennem hver rombe på denne liste og gøre det samme. Når vi er færdig, er vi få: en masse prikker, en for hver rombe.

![Punktdiagram afbildning](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Tegne modellen gennem datapunkterne

Nu hvis du ser på de prikker og squint, samlingen ser ud som en fat, uskarpt linje. Vi kan få vores markør og tegne en lige streg gennem den.

Ved at tegne en streg, oprettede vi en *model*. Tænk på dette som tager den virkelige verden og foretage en simplistic tegneserie version af filen. Nu er der galt på tegneserie - linjen går ikke igennem alle datapunkter. Men det er nyttigt forenkle.

![Simpel lineær regression](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

At alle prikkerne ikke præcis gennemfører linjen er OK. Data forskere beskriver dette ved at sige, at der er den model -, er linjen - og derefter hvert punkt har nogle *støj* eller *afvigelse* er knyttet til den. Der er den underliggende perfekte relation, og der er verden med, reelt tal, der tilføjer støj og sikkerhed.

Da vi forsøger at besvare spørgsmålene *hvor meget?* dette kaldes en *regressionsanalyse*. Og fordi vi bruger en lige streg, det er en *lineær regression*.

## <a name="use-the-model-to-find-the-answer"></a>Brug af for at finde svar

Nu har vi en model, og vi stil den vores spørgsmål: hvor meget koster en 1.35 karat rombe?

Hvis du vil besvare vores spørgsmål, skal vi overskue 1.35 carats og tegne en lodret streg. Hvor den krydser linjen model overskue vi en vandret streg på kr akse. Det rammer lige ved 10.000. Udligger! Det var svaret: en 1.35 karat rombe omkostninger om $10.000.

![Finde svar på modellen](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Oprette et konfidensinterval

Det er naturligt at undrer dig over, hvordan præcis denne Tekstfuldførelse er. Det er praktisk at vide om 1.35 karat diamanten bliver meget tæt på DKK 10.000, eller meget højere eller lavere. Hvis du vil finde ud af, Lad os tegne en konvolut omkring en regression, der indeholder de fleste af prikkerne. Denne konvolut hedder vores *konfidensinterval*: Vi er ret sikker på, at priser falder inden for denne konvolut, fordi i den tidligere de fleste af dem har. Vi kan tegne to mere vandrette streger fra hvor 1.35 karat linjen krydser øverst og nederst på konvolutten.

![Konfidensinterval](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kan vi sige noget om vores konfidensinterval: Vi kan sige sikker måde, som prisen på en 1.35 karat rombe er om $10.000 – men det kan være så lave som kr 8.000 og det kan være så høj som kr 12.000.

## <a name="were-done-with-no-math-or-computers"></a>Vi er færdig, med ingen matematisk eller -computere

Vi har hvilken data forskere bliver betalt til at gøre, og vi har det ved at trække:

* Vi har stillet et spørgsmål, at vi kan besvare med data
* Vi har oprettet en *model* ved hjælp af *lineær regression*
* Vi har foretaget en *forudsigelse*, skal du fuldføre med et *konfidensinterval*

Og vi bruge ikke matematisk eller computere til at gøre den.

Nu, hvis vi har haft mere, ønsker...

* klip af diamanten
* farvevariationer (hvor tæt diamanten er at blive hvid)
* antallet af optagelse i diamanten

.. .Klik derefter vi ville have haft flere kolonner. Matematisk bliver i så fald nyttige. Hvis du har mere end to kolonner, er det svært at tegne prikker på papir. Beregningerne kan du meget fint passer til linjen eller dette plan til dine data.

Vi har to tusinde eller to millioner også, hvis i stedet for blot en håndfuld ruder, og derefter kan du gøre, der fungerer meget hurtigere med en computer.

I dag, vi har talt om, hvordan du gør lineær regression, og vi har foretaget en forudsigelse ved hjælp af data.

Sørg for at se de andre videoer i "Data videnskabelige for begyndere" fra Microsoft Azure Machine Learning.



## <a name="next-steps"></a>Næste trin

  * [Prøv en første data videnskabelige forsøg med Machine Learning Studio](machine-learning-create-experiment.md)
  * [Få en introduktion til Machine Learning på Microsoft Azure](machine-learning-what-is-machine-learning.md)
