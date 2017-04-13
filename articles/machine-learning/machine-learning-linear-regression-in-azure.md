<properties 
    pageTitle="Brug af lineære Regression i Machine Learning | Microsoft Azure" 
    description="En sammenligning af lineære regression datamodeller i Excel og i Azure Machine Learning Studio" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>Brug af lineære regression i Azure Machine Learning

> *Kate Baroni* og *Ben Boatman* er enterprise løsningsarkitekter i Microsoft-Data indsigt Center of Excellence. I denne artikel beskrives de brugernes oplevelse, overføre en eksisterende regression analyse pakke til en skybaseret løsning ved hjælp af Azure Machine Learning.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Mål:

Vores projekt, der er i gang med to mål:  

1. Brug skønnet analytics til at forbedre nøjagtigheden af vores organisation månedlige indtægter prognoser  
2. Brug Azure ML til at bekræfte, optimere, øge overførselshastighed, og skalering af vores resultater.  

Som mange virksomheder går vores organisation gennem en månedlige indtægter prognoser proces. Vores lille gruppe på virksomhedsanalytikere blev opgave ved hjælp af Machine Learning til at understøtte processen og forbedre prognose nøjagtigheden.  Teamet brugt flere måneder indsamling af data fra flere kilder og afprøvet statistiske analyser identificere vigtige attributter, der er relevante for tjenester salg prognoser attributterne data.  Næste trin er at starte prototyper statistiske regression modeller på dataene i Excel.  I et par uger fik vi en Excel regressionsmodel, der outperforming det aktuelle felt og økonomi prognoser processer. Dette blev oprindeligt forudsigelse resultatet.  


Vi derefter tog det næste trin at flytte vores skønnet analytics hen til Azure ML til at finde ud af, hvordan Azure ML kan forbedre på skønnet ydeevne.


## <a name="achieving-predictive-performance-parity"></a>Nå fungerer ensartet skønnet ydeevne

Vores første prioritet var at opnå fungerer ensartet mellem Azure ML og Excel regression modeller.  Givet lige præcis de samme data, og den samme opdeling til uddannelse og test af data, som vi ville opnå skønnet ydeevne fungerer ensartet mellem Excel og Azure ML.   Har mislykkedes vi. Excel-datamodel langt bedre Azure ML modellen.   Fejlen blev på grund af manglende forståelse af indstillingen grundlæggende værktøj i Azure ML. Efter en synkronisering med produktteamet Azure ML vi erfaringer en bedre forståelse af base konfiguration kræves til vores datasæt og opnået fungerer ensartet mellem de to modeller.  

### <a name="create-regression-model-in-excel"></a>Oprette regressionsmodel i Excel
Vores Excel Regression bruges den standard lineære regressionsmodel, der findes i Excel Analysis ToolPak. 

Vi beregnes *betyder absolutte % fejl* og brugt som målingen ydeevne for modellen.  Det tog 3 måneder at nå frem til en arbejdsmodel ved hjælp af Excel.  Vi bragt meget af læring til Azure ML forsøget som i sidste ende er nyttige i forstå krav.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Oprette tilsvarende forsøg i Azure Machine Learning  
Vi har fulgt disse trin for at oprette vores forsøg i Azure ML:  

1.  Der er overført datasættet som en CSV-fil til Azure ML (meget lille-fil)
2.  Oprettes en ny forsøg og bruges [Vælg kolonner i Dataset] [ select-columns] modul til at vælge de samme datafunktioner, der bruges i Excel   
3.  Bruges de [Opdelte Data] [ split] -modul (med *Relative udtryk* tilstand) til at opdele dataene i nøjagtige samme tog sæt, som var blevet udført i Excel  
4.  Experimented med den [Lineære Regression] [ linear-regression] modul (kun standardindstillinger) dokumenteret og sammenlignes resultaterne til vores Excel regressionsmodel

### <a name="review-initial-results"></a>Gennemse resultaterne af indledende
I første omgang, langt Excel datamodel klart bedre Azure ML model:  

|   |Excel|Azure ML|
|---|:---:|:---:|
|Ydeevne|   |  |
|<ul style="list-style-type: none;"><li>Justeret R firkant</li></ul>| 0,96 |I/T.|
|<ul style="list-style-type: none;"><li>Koefficient for <br />Bestemmelse</li></ul>|I/T.|   0.78<br />(lav nøjagtigheden)|
|Betyde absolutte fejl |  $9. 5M|  $ 19.4 M|
|Betyde absolutte fejl (%)|   6.03%|  12.2%

Når vi stødte vores proces og resultater af udviklere og data forskere Azure ML teammedlemmer, medtaget de hurtigt nogle nyttige tip.  

* Når du bruger den [Lineære Regression] [ linear-regression] modul i Azure ML, der leveres to metoder:
    *  Online graduering afstamning: Der er mere passende for større skalering problemer
    *  Almindelige mindste kvadrater: Dette er de fleste tænke på, når de hører lineær regression metode. Almindelige mindste kvadrater kan være et mere optimal valg for small datasæt.
*  Overvej at tilpasse parameteren L2 forbindelse vægt for at forbedre ydeevnen. Det er angivet til 0,001 som standard og for vores small datasæt, vi angive den til 0,005 til at forbedre ydeevnen.    

### <a name="mystery-solved"></a>Mystery løser!
Når vi har anvendt anbefalingerne, kan vi opnået samme oprindelige ydeevnen i Azure ML som med Excel:   

|| Excel|Azure ML (indledende)|Azure ML m / mindste kvadrater|
|---|:---:|:---:|:---:|
|Mærket værdi  |Faktiske oplysninger (numerisk)|samme|samme|
|Læring  |Excel -> Data Analysis -> Regression|Lineær Regression.|Lineær Regression|
|Læring indstillinger|I/T.|Som standard|almindelige mindste kvadrater<br />L2 = 0,005|
|Datasæt|26 rækker, 3 funktioner, 1 etiket.   Alle numeriske.|samme|samme|
|Opdel: tog|Excel oplæring i de første 18 rækker, testet i de sidste 8 rækker.|samme|samme|
|Opdel: Test|Formler i Excel og regression anvendes på de sidste 8 rækker|samme|samme|
|**Ydeevne**||||
|Justeret R firkant|0,96|I/T.||
|Koefficient for bestemmelse|I/T.|0.78|0.952049|
|Betyde absolutte fejl |$9. 5M|$ 19.4 M|$9. 5M|
|Betyde absolutte fejl (%)|<span style="background-color: 00FF00;">6.03%</span>|12.2%|<span style="background-color: 00FF00;">6.03%</span>|

Desuden sammenlignet Excel koefficienter godt med funktion tykkelser i Azure erfaren modellen:

||Excel koefficienter|Azure funktion tykkelser|
|---|:---:|:---:|
|SKÆRING/systematiske fejl|19470209.88|19328500|
|Funktionen A|0.832653063|0.834156|
|Funktionen B|11071967.08|11007300|
|Funktionen C|25383318.09|25140800|

## <a name="next-steps"></a>Næste trin

Vi ville bruge Azure ML webtjeneste i Microsoft Excel.  Vores virksomhedsanalytikere, der er afhængige af Excel, og vi behov en metode til at ringe til webtjenesten Azure ML med en række med Excel-data og har den returværdi estimerede til Excel.   

Vi også gerne optimere vores model ved hjælp af indstillinger og algoritmer, der er tilgængelige i Azure ML.

### <a name="integration-with-excel"></a>Integration med Excel
Vores løsning var at giver muligheder for vores Azure ML regressionsmodel ved at oprette en webtjeneste fra erfaren modellen.  Webtjenesten blev oprettet i et par minutter, og vi kunne kald den direkte fra Excel til at returnere værdien estimerede indtægter.    

Sektionen *Web Services-Dashboard* omfatter en kan downloades Excel-projektmappe.  Projektmappen indeholder forudformaterede web API og -Skemafiler oplysningerne om tjenesten integreret.   Når du klikker på *Hent Excel-projektmappe*, åbnes, og du kan gemme den til din lokale computer.    

![][1]
 
Kopiere din foruddefinerede parametre i sektionen blå Parameter sammen med projektmappen åben, som vist nedenfor.  Når parametrene, der er angivet, Excel-opkald til webtjenesten AzureML og karakter for den estimerede vises etiketter i sektionen grønne forudsagte værdier.  Projektmappen fortsætter med at oprette prognoser for parametre, der er baseret på din erfaren model for alle rækkeelementer, der er angivet under parametre.   Du kan finde flere oplysninger om, hvordan du bruger denne funktion kan du se [forbrug en Azure Machine Learning webtjeneste fra Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Optimering og yderligere forsøg
Nu hvor vi har en oprindelig plan med vores Excel-datamodel, flyttes vi frem til at optimere vores Azure ML lineær Regression Model.  Vi har brugt modulet [Filter-baserede valg af funktion] [ filter-based-feature-selection] du kan forbedre vores datavalg på indledende elementer og det hjælper os med at opnå en bedre ydeevne 4.6% betyder absolutte fejl.   Vi vil bruge denne funktion, som kan gemme os uger i gentagelse gennem dataattributter til at finde det rigtige sæt af funktioner, der skal bruges til modellering for fremtidige projekter.  

Næste vi ønsker at medtage yderligere algoritmer som [Bayesian] [ bayesian-linear-regression] eller [Øget beslutningstræer] [ boosted-decision-tree-regression] i vores forsøg at sammenligne ydeevne.    

Hvis du vil eksperimentere med regression, er en god datasæt til at prøve energi effektivitet Regression eksempel datasæt, som har masser af numeriske attributter. Datasættet er angivet som en del af eksempel datasæt i ML Studio.  Du kan bruge en lang række læ moduler til at forudsige opvarmning indlæsning eller køling indlæse.  Nedenstående diagram er en ydeevne sammenligning af forskellige regression lærer mod energieffektivitet dataset forudsigelser for variablen target kølesystem Indlæs: 

|Model|Betyde absolutte fejl|Rod middelværdi kvadrerede fejl|Relative, absolutte fejl|Relativ kvadrerede fejl|Koefficient for bestemmelse
|---|---|---|---|---|---
|Øget beslutningstræ|0.930113|1.4239|0.106647|0.021662|0.978338
|Lineær Regression (graduering afstamning)|2.035693|2.98006|0.233414|0.094881|0.905119
|Neural netværk Regression|1.548195|2.114617|0.177517|0.047774|0.952226
|Lineær Regression (ordinære mindste kvadrater)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Vigtige Takeaways 

Vi har lært meget ved fra kører Excel regression og Azure Machine Learning forsøg parallelt. Oprette den oprindelige model i Excel og sammenligne dem med modellerne ved hjælp af Azure ML [Lineær Regression] [ linear-regression] sammen hjulpet os med at få mere at vide Azure ML, og vi har opdaget muligheder for at forbedre ydeevnen i data markering og model.         

Vi fandt også, at det anbefales at bruge [Filter-baserede valg af funktion] [ filter-based-feature-selection] at fremskynde fremtidige forudsigelse projekter.  Ved at anvende valg af funktion til dine data, kan du oprette en forbedret model i Azure ML med bedre overordnede ydeevne. 

Muligheden for at overføre den skønnet analytisk prognoser fra Azure ML til Excel systemically gør det muligt for en stigning i muligheden for at give korrekt resultater til en bred business bruger målgruppe.     


## <a name="resources"></a>Ressourcer
Nogle ressourcer, der er angivet for hjælp til at du arbejder med regression:  

* Regressionsanalyse i Excel.  Hvis du har aldrig forsøgt regressionsanalyse i Excel, selvstudiet gør det nemt: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regression vs prognoser.  Tyler Chessman skrevet en blogartikel forklarer, hvordan du kan tid serie prognoser i Excel, som indeholder en god begynder beskrivelse af lineære regression. [http://sqlmag.com/SQL-Server-Analysis-Services/understanding-time-Series-Forecasting-Concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Almindelige mindste kvadrater lineær Regression: Fejl, problemer og faldgruber.  For en introduktion og diskussion regressionstyper: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
