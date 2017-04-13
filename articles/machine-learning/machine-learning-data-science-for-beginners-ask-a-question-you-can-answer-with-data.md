<properties
   pageTitle="Stil et spørgsmål, kan du besvare udforme spørgsmål med data - | Microsoft Azure"
   description="Lær, hvordan udforme en data videnskabelige spørgsmål i Data videnskabelige for begyndere video 3. Indeholder en sammenligning af klassificering og regression spørgsmål."
   keywords="data videnskabelige spørgsmål, udforme spørgsmål, regression spørgsmål og klassifikation spørgsmål, skarpe spørgsmål"
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

# <a name="ask-a-question-you-can-answer-with-data"></a>Stil et spørgsmål, kan du besvare med data

## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Data videnskabelige for begyndere serie

Lær, hvordan udforme en data videnskabelige spørgsmål i Data videnskabelige for begyndere video 3. Denne video indeholder en sammenligning af spørgsmål til klassificering og regression algoritmer.

For at få mest muligt ud af serien skal du se dem alle. [Gå til listen over videoerne](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>Andre videoer i denne serie

*Data videnskabelige for begyndere* er en hurtig introduktion til data videnskab i fem korte videoer.

  * Video 1: [5 spørgsmål data videnskabelige svar](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
  * Video 2: [er dine data er du klar til data videnskabelige?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
  * Video 3: Stil et spørgsmål, kan du besvare med data
  * Video 4: [Forudsige et svar med en enkelt model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek.)*
  * Video 5: [Kopiere andres arbejde for at gøre data videnskabelige](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Afskrift: Stil et spørgsmål, kan du besvare med data

Velkommen til tredje videoen i serien "Data videnskabelige for begyndere."  

I denne database får du nogle tip til udarbejdelsen et spørgsmål, kan du besvare med data.

Du kan få mere ud af denne video, hvis du først se de to tidligere videoer i denne serie: "5 spørgsmål data videnskabelige kan besvare" og "Er dine data er klar til data videnskabelige"?

## <a name="ask-a-sharp-question"></a>Stil et skarpe spørgsmål

Vi har talt om, hvordan data videnskabelige processen med at bruge navne (også kaldet kategorier eller etiketter) og tal/bogstaver til at forudsige et svar på et spørgsmål. Men den kan ikke blot et spørgsmål det skal være en *skarpe spørgsmål.*

Et vagt spørgsmål skal ikke kunne svare med et navn eller et tal. Et skarpe spørgsmål skal.

Forestil dig, at du har fundet en magiske pære med en genie som truthfully vil besvare en hvilken som helst du stiller spørgsmål. Men det er en mischievous genie, og hun får forsøger at foretage sin answer som uklare og overskuelig, som han kan slippe med. Du vil fastgøre ham med et spørgsmål, der er så lufttæt, han ikke kan hjælpe men fortælle dig, hvad du gerne vil vide.

Hvis du skulle stil et uklare spørgsmål som "Hvad der skal ske med min aktie?", genie kan besvare, "pris ændres". Det er en være svar, men er ikke særdeles nyttigt.

Men hvis du skulle bede en skarpe spørgsmål som "Hvad min aktie salgsprisen bliver næste uge?", genie kan ikke hjælpe men give dig en bestemt svar og forudsige en salgsprisen.

## <a name="examples-of-your-answer-target-data"></a>Eksempler på dit svar: målrette data

Når du beskrive dit spørgsmål, kan du se for at se, om du har eksempler på svaret i dine data.

Hvis vores spørgsmål er "Hvad min aktie salgsprisen bliver næste uge?" derefter har vi at sikre, at vores data indeholder aktiekurser pris oversigten.

Hvis vores spørgsmål er "hvilke bil i min flåden der skal mislykkes først?" derefter har vi at sikre, at dine data indeholder oplysninger om forrige mislykkede forsøg.

![Målrette data - eksempler på dit svar. Udforme en data videnskabelige spørgsmål.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Disse eksempler på svar kaldes en destination. Et mål er, hvad vi forsøger at forudsige om fremtidige datapunkter, uanset om det er en kategori eller et tal.

Hvis du ikke har nogen target data, skal du at få nogle. Du kan ikke finde svar på dine spørgsmål uden den.

## <a name="reformulate-your-question"></a>Reformulate dit spørgsmål

Nogle gange kan du omformulere dit spørgsmål for at få et mere nyttig svar.

Spørgsmålet "Er denne data punkt A eller B"? beregner den kategori (eller navn eller etiket) for noget. Vi bruger en *klassifikation algoritme*for at besvare den.

Spørgsmålet "Hvor meget?" eller "Hvor mange?" Beregner et beløb. Vi bruger en *regression algoritme*for at besvare den.

Hvis du vil se, hvordan vi kan forvandle disse, Lad os se på spørgsmål, "historie hvilke nyheder er mest interessante for denne læser?" Den beder om at få en forudsigelse af et enkelt valg fra mange muligheder - med andre ord "Er denne A eller B eller C og D"? - og gerne vil bruge en klassifikation algoritme.

Men dette spørgsmål kan være nemmere at besvare, hvis du omformulere den som "hvordan interessant er hver tekstenhed på denne liste til denne læser?" Nu kan du give hver artikel et numerisk resultat, og derefter er det nemt at identificere artiklen højeste pointmodel for kundeemner. Dette er en omformulere klassifikation spørgsmål i et regression spørgsmål eller hvor meget?

![Reformulate dit spørgsmål. Klassifikation spørgsmål kontra regression spørgsmål.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Hvordan du stiller et spørgsmål er dette hvilke algoritmen med kan give dig et svar.

Du finder, at visse familier af algoritmer - som dem i eksemplet nyheder historie - er tæt forbundne. Du kan reformulate dit spørgsmål for at bruge den algoritme, får du mest nyttige svaret.

Men, de vigtigste stil denne skarpe spørgsmål - det spørgsmål, som du kan besvare med data. Og Sørg for, at du har de rigtige data til at besvare den.

Vi har talt om nogle grundlæggende principper for stille et spørgsmål, kan du besvare med data.

Sørg for at se de andre videoer i "Data videnskabelige for begyndere" fra Microsoft Azure Machine Learning.


## <a name="next-steps"></a>Næste trin

  * [Prøv en første data videnskabelige forsøg med Machine Learning Studio](machine-learning-create-experiment.md)
  * [Få en introduktion til Machine Learning på Microsoft Azure](machine-learning-what-is-machine-learning.md)
