<properties
   pageTitle="5 data videnskabelige spørgsmål – Data videnskabelige for begyndere | Microsoft Azure"
   description="Få en hurtig introduktion til videnskabelige data fra Data videnskabelige for begyndere, fem korte videoer, der starter med feltet 5 spørgsmål Data videnskab og svar."
   keywords="at gøre data videnskabelige, data videnskabelige begyndere, data videnskabelige for begyndere, typer spørgsmål, data videnskabelige spørgsmål, data videnskabelige video"
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

# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Data videnskabelige for begyndere video 1: feltet 5 spørgsmål data videnskabelige svar

Få en hurtig introduktion til videnskabelige data fra *Data videnskabelige for begyndere* i fem korte videoer fra en øverste data scientist. Disse videoer er grundlæggende men nyttige, uanset om du er interesseret i at gøre data videnskabelige eller du arbejder med data forskere.

Denne første video er om forskellige typer spørgsmål, som data videnskabelige kan besvares. For at få mest muligt ud af serien skal du se dem alle. [Gå til listen over videoerne](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-the-5-questions-data-science-answers]

## <a name="other-videos-in-this-series"></a>Andre videoer i denne serie

*Data videnskabelige for begyndere* er en hurtig introduktion til data videnskabelige tager om 25 minutter samlede. Se de fire videoer:

  * Video 1: 5 spørgsmål data videnskabelige svar
  * Video 2: [er dine data er du klar til data videnskabelige?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
  * Video 3: [Stil et spørgsmål, kan du besvare med data](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek.)*
  * Video 4: [Forudsige et svar med en enkelt model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek.)*
  * Video 5: [Kopiere andres arbejde for at gøre data videnskabelige](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Afskrift: 5 spørgsmål data videnskabelige svar

Hej! Velkommen til videoserie *Data videnskabelige for begyndere*.

Data naturvidenskab kan være skræmmende, så jeg vil introducere grundlæggende uden brug af ligninger eller computer jargon programming.

I denne første video gennemgår vi om "på 5 spørgsmål data videnskabelige svar".

Data videnskabelige bruger og -navne (også kendt som kategorier eller etiketter) til at forudsige svar på spørgsmål.

Det kan Overrask dig, men *der er kun fem spørgsmål, der data videnskabelige svar*:

  * Er dette A eller B?
  * Er dette mærkelig ud?
  * Hvor meget – eller – hvor mange?
  * Hvordan er det organiseret?
  * Hvad skal jeg gøre ud?

  Hver enkelt af disse spørgsmål besvares af en separat familie af machine learning metoder, kaldet algoritmer.


Det er praktisk at tænke en algoritme som en opskrifter osv og dine data som bestanddele. En algoritme kan se, hvordan du kan kombinere og blande dataene, hvis du vil have et svar. Computere, der svarer til en blender. De gør mest muligt ud af det hårde arbejde af algoritmen for dig, og de gør det ret hurtig.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Spørgsmål 1: Er denne A eller B? bruger klassifikation algoritmer

Lad os starte med spørgsmålet: er denne A eller B?

![Klassifikation algoritmer: er denne A eller B?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-classification-algorithms.png)

I denne serie af algoritmer kaldes to klasse klassificering.

Det er praktisk til en hvilken som helst spørgsmål, der indeholder lige to mulige svar.

Eksempel:

  * Mislykkes denne dæk i de næste 1.000 sømil: Ja eller Nej?
  * Som samler på flere kunder: en $5 kupon eller 25% rabat?

Dette spørgsmål kan også rephrased for at medtage mere end to valgmuligheder: er denne A eller B eller C og D, osv.?  Dette kaldes multiclass klassifikation og det er nyttigt, når du har flere – eller flere tusinder – mulige svar. Multiclass klassificering vælger sandsynligvis en.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Spørgsmål 2: Er dette mærkelig ud? bruger anomali registrering algoritmer

Er det næste data videnskabelige kan besvare spørgsmål: er denne besynderligt? Dette spørgsmål besvares af en familie af algoritmer kaldet anomali registrering.

![Anomali registrering algoritmer: er denne besynderligt?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-anomaly-detection-algorithms.png)


Hvis du har et kreditkort, har du allerede benefitted fra anomali registrering. Virksomheden kreditkort analyserer dit køb mønstre, så de kan give dig besked om mulige svindel. Gebyrer, der er "mærkelig ud" kan være et køb en butik, hvor du normalt ikke køb eller købe et meget dyr element.

Dette spørgsmål kan være praktiske på mange måder. For eksempel:

  * Hvis du har en bil med tryk målere, skal du måske at kende: er denne tryk måler læse normal?
  * Hvis du overvåger internettet, vil du gerne vil vide: er denne meddelelse fra internettet typisk?

Registrering af anomali markerer uventede eller usædvanlige begivenheder eller funktionsmåder. Det giver tip hvor der skal søges efter problemer.



## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Spørgsmål 3: Hvor meget? eller hvor mange? bruger regression algoritmer

Machine learning kan også forudsige svaret på hvordan meget? eller hvor mange? Algoritme familien, finder du svar på spørgsmålet kaldes regression.

![Regression algoritmer: hvor meget? eller hvor mange?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-regression-algorithms.png)


Regression algoritmer foretage numeriske prognoser, som f.eks.:

  * Hvad vil temperaturen være næste tirsdag?  
  * Hvad er mit fjerde kvartals salg?

De gør det nemmere besvare spørgsmål, der beder om et tal.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Spørgsmål 4: Hvordan er det organiseret? bruger klynge algoritmer

Nu er de sidste to spørgsmål lidt mere avanceret.

Nogle gange du vil forstå strukturen i et datasæt - hvordan er det organiseret? For dette spørgsmål behøver du ikke eksempler, som du allerede kender udfald for.

Der findes mange måder at test af strukturen af data. En metode klynge. Det adskiller data i neutralt "klumper sammen," til nemmere fortolkning. Med klynge er der ingen rigtige resultat.

![Klynge algoritmer: hvordan er det organiseret?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-clustering-algorithms.png)

Almindelige eksempler på klyngedannelse spørgsmål er:

  * Hvilke fremvisere have de samme typer film?
  * Hvilken printermodeller mislykkes på samme måde?

Ved at forstå, hvordan data er organiseret, kan du bedre forstå - og forudsige - funktionsmåder og begivenheder.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Spørgsmål 5: Hvad skal jeg gøre nu? bruger styrke læ algoritmer

Sidst spørgsmål – Hvad skal jeg gøre nu? – bruger en serie af algoritmer kaldet styrke learning.

Styrke learning blev inspireret af hvordan hjerner af rotter og mennesker svare på straf og belønninger. Disse algoritmer lære af resultater, og beslut på den næste handling.

Styrke learning er typisk passer til automatiseret systemer, der har at gøre mange små beslutninger uden human vejledning.

![Styrke læ algoritmer: Hvad skal jeg gøre ud?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-reinforcement-learning-algorithms.png)

Spørgsmål finder du svar på den er altid om, hvilken handling skal blive ført – som regel ved en computer eller en robot. Eksempler kan nævnes:

  * Hvis jeg er en temperaturregulator for et hus: justere temperaturen til eller forlader det sted, hvor det er?  
  * Hvis jeg er en selvstændig fører bil: på et gult lys bremse eller fremskynde?  
  * For en robot vakuum: Behold vacuuming, eller gå tilbage til den afgifts station?

Styrke learning algoritmer indsamle data, som de går, læ fra prøve.

Feltet 5 spørgsmål data videnskabelige kan besvare, så det var det.



## <a name="next-steps"></a>Næste trin

  * [Prøv en første data videnskabelige forsøg med Machine Learning Studio](machine-learning-create-experiment.md)
  * [Få en introduktion til Machine Learning på Microsoft Azure](machine-learning-what-is-machine-learning.md)
