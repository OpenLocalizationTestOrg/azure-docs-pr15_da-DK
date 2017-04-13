<properties
   pageTitle="Er dine data er du klar til videnskabelige data? Data evaluering | Microsoft Azure"
   description="Få mere at vide 4 kriterierne for data skal være klar til videnskabelige data. Data videnskabelige for begyndere video 2 har cement eksempler, der kan hjælpe dig med grundlæggende data evaluering."
   keywords="relevante data evaluere data, forberede data, data kriterier, data, der er klar"
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


# <a name="is-your-data-ready-for-data-science"></a>Er dine data er du klar til videnskabelige data?

## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Data videnskabelige for begyndere serie

Lær at evaluere dine data for at sikre, at den opfylder grundlæggende kriterier for at være klar til videnskabelige data.

For at få mest muligt ud af serien skal du se dem alle. [Gå til listen over videoerne](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## <a name="other-videos-in-this-series"></a>Andre videoer i denne serie

*Data videnskabelige for begyndere* er en hurtig introduktion til data videnskab i fem korte videoer.

  * Video 1: [5 spørgsmål data videnskabelige svar](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
  * Video 2: Er dine data er du klar til videnskabelige data?
  * Video 3: [Stil et spørgsmål, kan du besvare med data](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek.)*
  * Video 4: [Forudsige et svar med en enkelt model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek.)*
  * Video 5: [Kopiere andres arbejde for at gøre data videnskabelige](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Afskrift: Er dine data er du klar til videnskabelige data?

Velkommen til "Er dine data er du klar til data videnskabelige?" den anden video i serien *Data videnskabelige for begyndere*.  

Før data videnskabelige kan give dig de svar, du vil, har du give den nogle høj kvalitet råvarer til at arbejde med. Ligesom foretage en pizza, jo bedre bestanddele du starter med, desto bedre det endelige produkt.

## <a name="criteria-for-data"></a>Kriterier for data

Så, tale om data videnskab er der nogle bestanddele, som vi skal adskille sammen.

Vi har brug for data, som er:

  * Relevante
  * Forbindelse
  * Nøjagtige
  * Nok til at arbejde med

## <a name="is-your-data-relevant"></a>Er dine data relevante?

Så første datablad - vi skal bruge data, der er relevant.

![Relevante data kontra irrelevante data - evaluere data](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Se tabellen på venstre side. Vi opfyldt syv personer uden for Boston søjler, målt niveauerne overvågning alkohol, rød Sox batting gennemsnit i deres sidste game og pris mælk i den nærmeste butik.

Dette er alle perfekt gyldige data. Det er kun fejl er, at det ikke er relevant. Der er ingen indlysende relation mellem disse tal. Hvis jeg har givet dig den aktuelle pris af og rød Sox batting gennemsnittet, findes der ingen måde, du kunne gætte mit overvågning alkoholindhold.

Se nu på tabellen i højre side. Denne gang vi målt hver persons brødtekst masse og tælles antallet af drikkevarer, de har haft.  Tallene i hver række er nu relevante til hinanden. Hvis jeg har givet dig min brødtekst masse og antallet af Margaritas, jeg har haft, kan du gøre et gæt på min overvågning alkohol indhold.

## <a name="do-you-have-connected-data"></a>Du har knyttet data?

Den næste datablad er forbundne data.

![Forbundne data kontra afbrudt data - kriterier, skal du data data, der er klar](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Her er nogle relevante data om kvaliteten af hamburgers: Tænd temperaturen, bøf tykkelse og rangering i den lokale mad magazine. Men bemærk mellemrummet i tabellen i venstre side.

De fleste datasæt mangler nogle værdier. Det er fælles for har huller således og måder til at løse dem. Men hvis der er for stor mangler, dine data begynder at ligne schweizisk ost.

Hvis du ser på tabellen i venstre side, er der så mange manglende data, det er svært at komme frem til en hvilken som helst type relation mellem rist temperatur og bøf tykkelse. Dette er et eksempel på afbrudt data.

Tabellen i højre side er fyldt dog og fuldføre - et eksempel på forbundne data.

## <a name="is-your-data-accurate"></a>Er dine data nøjagtigt?

Den næste datablad vi brug for er nøjagtigheden. Her er fire destinationer, som vi vil gerne med pile, der påløber.

![Nøjagtige data kontra forkerte data - data kriterier](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Se på destinationen i øverste højre hjørne. Vi har en tæt gruppering højre omkring bullseye. Det, er naturligvis nøjagtige. Unormalt, på sproget af data videnskabelige vores ydeevne i target højre side under den også betragtes som nøjagtige.

Hvis du skulle kortlægge midten af disse pile, skal du se, at det er meget tæt på bullseye. Pilene spredes alle omkring destinationen, så de er betragtes som upræcise, men de er centreret omkring bullseye, så de er betragtes som nøjagtige.

Se nu på destinationen øverst til venstre. Vores pile Tryk her meget tæt på hinanden, på en tæt gruppering. Det er præcis, men de er forkerte, fordi centreret er vej helt væk fra bullseye. Og pile i nederste venstre destinationen er naturligvis både forkerte og upræcise. Denne anker skal flere øvelse.

## <a name="do-you-have-enough-data-to-work-with"></a>Har du nok data til at arbejde med?

Til sidst skal datablad #4 – vi skal have nok data.

![Har du nok data til analyse? Evaluering af data](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Tænk på hvert datapunkt i tabellen som værende et penselstrøg i et maleri. Hvis du har nogle af dem, maleobjekt kan være ret uskarpt – det er svært at se, hvad den bruges.

Hvis du føjer nogle flere pensel streger, starter din maleri at få en smule skarpere.

Når du har store nok streger, kan du se lige nok til at foretage nogle generelle beslutninger. Er det et sted, hvor jeg måske at besøge? Det ser lyst, der ligner rent vand – Ja, der er, hvor jeg vil på ferie.

Når du tilføjer flere data, billedet, bliver mere klart, og du kan foretage mere detaljerede beslutninger. Nu kan jeg se på de tre hoteller på venstre banken. Du kender, jeg virkelig som arkitektur af i forgrunden. Jeg vil blive der, i den tredje Minimumgrænse.

Med data, som er relevant, forbundne, nøjagtige og nok, vi har alle de bestanddele, vi har brug for at gøre nogle høj kvalitet data videnskabelige.

Sørg for at se de andre fire videoer i *Data videnskabelige for begyndere* fra Microsoft Azure Machine Learning.




## <a name="next-steps"></a>Næste trin

  * [Prøv en første data videnskabelige forsøg med Machine Learning Studio](machine-learning-create-experiment.md)
  * [Få en introduktion til Machine Learning på Microsoft Azure](machine-learning-what-is-machine-learning.md)
