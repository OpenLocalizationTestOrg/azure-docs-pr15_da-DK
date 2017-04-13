<properties 
    pageTitle="Evaluere model ydeevne i Machine Learning | Microsoft Azure" 
    description="Beskriver, hvordan du evaluere model ydeevne i Azure Machine Learning." 
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
    ms.date="08/19/2016" 
    ms.author="bradsev;garye" />


# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Sådan evaluere model ydeevne i Azure Machine Learning

Dette emne viser, hvordan du evaluere ydeevnen for en model i Azure Machine Learning Studio og giver en kort beskrivelse af de tilgængelige målepunkter til denne opgave. Tre almindelige tilsyn learning scenarier præsenteres: 

* simple lineære regression
* binær klassifikation 
* multiclass klassificering

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Evaluering af ydeevnen for en model, er en af de grundlæggende faser igangværende videnskabelige data. Det angiver, hvordan vellykkede er de resultat (prognoser) af et datasæt ved en erfaren model. 

Azure maskine læ understøtter model evaluering gennem to af dens primære machine learning-moduler: [Evaluere Model] [ evaluate-model] og [Tværs validere Model][cross-validate-model]. Disse moduler tillader dig at se, hvordan din model udfører med hensyn til et antal målepunkter, der ofte bruges i maskine læring og statistik.

##<a name="evaluation-vs-cross-validation"></a>Evaluering kontra tværs validering##
Evaluering og cross validering er almindelige måder at måle ydeevnen for din model. De begge genererer evaluering mål, som du kan undersøge eller sammenlignes med de andre modeller.

[Evaluere Model] [ evaluate-model] forventer et scored datasæt som input (eller 2 i tilfælde, du vil sammenligne ydeevnen for 2 forskellige modeller). Det betyder, at du har brug for til at undervise modellen ved hjælp af [Undervise Model] [ train-model] modul og foretager prognoser på nogle datasæt ved hjælp af [Karakteren Model] [ score-model] modulet, før du kan altid evaluere resultaterne. Evalueringen er baseret på de scored etiketter/sandsynligheder sammen med sand etiketterne, som alle er output ved [Karakteren Model] [ score-model] modul.

Du kan også bruge cross datavalidering til at udføre et antal tog karakteren evaluere handlinger (10 folder) automatisk på forskellige undersæt af inputdataene. De indtastede data er opdelt i 10 dele, hvor er en reserveret til test, og de andre 9 for kursus. Denne proces gentages 10 gange, og der beregnes gennemsnit evaluering målepunkter. Dette hjælper med at ved fastlæggelse af hvorvidt en model ville generalize til nye datasæt. [Validere tværs Model] [ cross-validate-model] modul i en baseret model og nogle mærket dataset og viser evalueringsresultater for hver af de 10 folder, ud over gennemsnitlig resultaterne.

I de følgende afsnit, skal vi oprette enkle regression og klassifikation modeller og vurdere deres ydeevne ved hjælp af begge [Evaluere Model] [ evaluate-model] og [Tværs validere Model] [ cross-validate-model] moduler.

##<a name="evaluating-a-regression-model"></a>Evaluering af en regressionsmodel##
Lad os antage, vi ønsker forudsige en bil pris ved hjælp af nogle funktioner som dimensioner, hestekræfter, specifikationer for program osv. Dette er et typisk regression problem, hvor variablen mål (*pris*) er en fortløbende numeriske værdi. Vi kan være en simpel lineær regressionsmodel, givet funktion værdier i en bestemt bil, kan forudsige pågældende bil pris. Denne regressionsmodel kan bruges til at give det samme dataset vi oplæring i. Når vi har de estimerede priser for alle biler, kan vi evaluere ydeevnen for modellen ved at kigge på hvor meget prognoser afviger fra den aktuelle pris i gennemsnit. For at illustrere, bruge vi *bil pris data (Raw) datasæt* tilgængelig i sektionen **Gemt datasæt** i Azure Machine Learning Studio.
 
###<a name="creating-the-experiment"></a>Oprette forsøget###
Tilføje følgende moduler til dit arbejdsområde i Azure Machine Learning Studio:

- Bil prisdata (Raw)
- [Lineær Regression][linear-regression]
- [Tog Model][train-model]
- [Resultat Model][score-model]
- [Evaluere Model][evaluate-model]


Oprette forbindelse til portene, som vist nedenfor i figur 1 og angive kolonnen navn i [Tog Model] [ train-model] modulet til *pris*.
 
![Evaluering af en regressionsmodel](media/machine-learning-evaluate-model-performance/1.png)

Figur 1. Evaluering af en regressionsmodel.

###<a name="inspecting-the-evaluation-results"></a>Undersøge evalueringsresultaterne###
Når du har kørt forsøget, kan du klikke på outputporten [Evaluere Model] [ evaluate-model] modul, og vælg *visuelle effekter* til at se evalueringsresultaterne,. Evaluering oplysningerne tilgængelige for regression modeller angives: *Betyder absolutte fejl*, *Rod betyder absolutte fejl*, *Relativ absolutte fejl*, *Relativ kvadrerede fejl*og *Koefficient bestemmelse*.

Ordet "fejl" her repræsenterer forskellen mellem den estimerede værdi og værdien SAND. Den absolutte værdi eller kvadratet af denne forskel beregnes som regel til at registrere det samlede omfanget af fejl på tværs af alle forekomster, som forskellen mellem den estimerede og sand værdi kan være negative i nogle tilfælde. Fejl målepunkter mål skønnet ydeevnen for en regressionsmodel med hensyn til dens prognoser fra SAND værdierne middelværdi afvigelse. Nederste fejlværdier betyder modellen er mere nøjagtig i at prognoser. En overordnede fejlmetrik på 0 betyder, at modellen passer perfekt til dataene.

Koefficient for bestemmelse, som er også kendt som R kvadrerede, er også et almindelige metode til at måle, hvor godt modellen passer til dataene. Det kan fortolkes som del af variation forklaring af modellen. En højere størrelsesforhold er bedre i dette tilfælde, hvor 1 angiver en perfekt.
 
![Lineær Regression evaluering målepunkter](media/machine-learning-evaluate-model-performance/2.png)

Figur 2. Lineær Regression evaluering målepunkter.

###<a name="using-cross-validation"></a>Ved hjælp af på tværs af datavalidering###
Som nævnt tidligere, du kan udføre gentagne undervisning, vundne og evaluering automatisk ved hjælp af [Tværs validere Model] [ cross-validate-model] modul. Alt, du skal i dette tilfælde er et datasæt, en baseret model og en [Kryds-validere Model] [ cross-validate-model] modul (se figuren nedenfor). Bemærk, at du skal angive kolonnen navn med *pris* i [Tværs validere Model] [ cross-validate-model] modulet egenskaber.

![Validere en regressionsmodel tværs](media/machine-learning-evaluate-model-performance/3.png)

Figur 3. Kryds-validere en regressionsmodel.

Når du har kørt forsøget, kan du undersøge evalueringsresultater ved at klikke på den højre outputport i [Tværs validere Model] [ cross-validate-model] modul. Dette får en detaljeret visning af målene for hver gentagelse (højformat) og gennemsnitlig resultaterne af hver af målepunkter (figur 4).
 
![Tværs validering resultaterne af en regressionsmodel](media/machine-learning-evaluate-model-performance/4.png)

Figur 4. Tværs validering resultaterne af en regressionsmodel.

##<a name="evaluating-a-binary-classification-model"></a>Evaluering af en binær klassifikation Model##
I et scenarie med binære klassifikation target variablen har kun to mulige resultater, for eksempel: {0, 1} eller {FALSK, SAND}, {negative, positive}. Antag, får du et datasæt af voksne medarbejdere med nogle demografiske og ansættelse variabler og, bliver du bedt om at forudsige niveauet indtægter, en binær variabel med værdierne {"< = 50K", "> 50K"}. Med andre ord klassen negative repræsenterer de medarbejdere, der gør mindre end eller lig med 50K om året, og klassen positive repræsenterer alle andre medarbejdere. Som i den simple lineære regression scenarie, vil vi undervise en model, resultat nogle data og evaluerer resultaterne. Den største forskel er valg af målepunkter Azure Machine Learning beregner og output. For at illustrere indtægter niveau forudsigelse scenarie, bruger vi [voksne](http://archive.ics.uci.edu/ml/datasets/Adult) datasættet til at oprette et Azure Machine Learning forsøg og evaluerer ydeevnen for en to-klasse logistisk regressionsmodel, en ofte anvendte binære klassificering.

###<a name="creating-the-experiment"></a>Oprette forsøget###
Tilføje følgende moduler til dit arbejdsområde i Azure Machine Learning Studio:

- Voksne tælling indtægter binære klassifikation datasæt
- [To-klasse logistisk Regression][two-class-logistic-regression]
- [Tog Model][train-model]
- [Resultat Model][score-model]
- [Evaluere Model][evaluate-model]

Oprette forbindelse til portene, som vist nedenfor i figur 5 og angive kolonnen navn i [Tog Model] [ train-model] modulet til *indtægter*.

![Evaluering af en binær klassifikation Model](media/machine-learning-evaluate-model-performance/5.png)

Figur 5. Evaluering af en binær klassifikation Model.

###<a name="inspecting-the-evaluation-results"></a>Undersøge evalueringsresultaterne###
Når du har kørt forsøget, kan du klikke på outputporten [Evaluere Model] [ evaluate-model] modul, og vælg *visuelle effekter* at få vist evalueringsresultater (Figur 7). Evaluering oplysningerne tilgængelige for binære klassifikation modeller angives: *nøjagtigheden*, *præcision*, *tilbagekalde*, *F1 resultat*og *AUC*. Desuden udskriver modulet en forvirring matrix, der viser antallet af SAND positive, FALSK negativer, falsk positive og sand negativer samt *ROC*, *Præcision/tilbagekaldelsen*og *løft* kurver.

Nøjagtigheden er blot andelen af korrekt klassificeret forekomster. Det er som regel den første metrikværdi, du ser på ved evaluering af en klassificering. Når testdata er dog skæve (hvor de fleste af forekomsterne, der hører til en af klasserne), eller du er mere interesseret i ydeevne på en af klasserne, nøjagtigheden virkelig ikke registrere effektiviteten i forbindelse med en klassificering. I indtægter niveau klassifikation scenarie antager du tester på nogle data, hvor 99% af forekomster repræsentere personer, som tjene mindre end eller lig med 50K om året. Det er muligt at opnå en 0.99 nøjagtighed ved at forudsige klassen "< = 50K" efter alle forekomster. I dette tilfælde vises klassificeringen til at udføre en god sag overordnede, men i virkeligheden, den undlader at klassificere en af de high-income personer (1-%) korrekt.

Derfor er det praktisk til at beregne flere statistikker, der registrerer mere specifikke aspekter af evalueringen. Før du skifter til oplysninger om disse målepunkter, er det vigtigt at forstå matrixen forvirring på en beregning i binær klassificering. Klasse etiketterne i sættet kursus kan foretage på kun 2 mulige værdier, som vi normalt refererer til som positiv eller negativ. De positive og negative forekomster, der beregner en klassificering korrekt hedder SAND positive (TP) og sand negativer (TN), henholdsvis. Forkert klassificeret forekomster kaldes også falsk positive (FP) og FALSK negativer (FN). Matrixen forvirring er blot en tabel, der viser antallet af forekomster, der falder under hver af disse 4 kategorier. Azure Machine Learning bestemmer automatisk hvilket af de to klasser i datasættet er positiv klassen. Hvis klasse etiketterne er boolesk eller heltal, der 'true' eller '1' mærket forekomster tildelt klassen positive. Hvis etiketterne er strenge, som er tilfældet med indtægter datasættet, etiketterne er sorteret i alfabetisk rækkefølge og det første niveau er valgt at være klassen negative, mens det andet niveau er positiv klassen.

![Binær klassifikation forvirring Matrix](media/machine-learning-evaluate-model-performance/6a.png)

Figur 6. Binær klassifikation forvirring Matrix.

Gå tilbage til indtægter klassifikation problemet, vil vi til at få flere evalueringsspørgsmål, der hjælper os med at forstå ydeevnen for den klassificering, der bruges. En meget neutralt spørgsmål er: ' af de personer, som modellen, der er skønsmæssigt beregnet til at være med indtjening > 50 K (TP + FP), hvor mange er klassificeret korrekt (TP)? " Dette spørgsmål kan besvares ved at kigge på den model, som er del af forekomster, der er klassificeret korrekt **præcision** : TP/(TP+FP). En anden almindelige spørgsmål er "med indtjening medarbejdere med indtægter af alle høj > 50 k (TP + FN), hvor mange klassificeringen klassificere korrekt (TP)". Dette er faktisk den **tilbagekalde**, eller den sande positive rente: TP/(TP+FN) for klassificeringen. Du oplever måske, at der er en indlysende forholdet mellem præcision og tilbagekaldelsen. For eksempel givet et relativt ikke-opgjorte datasæt, ville en klassificering, der beregner hovedsageligt positive forekomster, har en høj tilbagekaldelse, men vil klassificeret en hellere lav præcision som mange af de negative forekomster hvilket resulterer i et stort antal forkerte forekomster. Hvis du vil se en afbildning af hvordan disse to målepunkter variere, kan du klikke på præcision/TILBAGEKALDELSEN kurven på siden evaluering resultatet output (øverste venstre del af figur 7).

![Binær klassifikation evalueringsresultater](media/machine-learning-evaluate-model-performance/7.png) figur 7. Binær klassifikation evalueringsresultater.

En anden relaterede metrikværdi, der bruges ofte er **F1 resultat**, som tager både præcision og tilbagekaldelsen i betragtning. Det er det harmoniske gennemsnit af disse 2 målepunkter og beregnes således: F1 = 2 (tilbagekaldelsen præcision x) / (præcision + tilbagekaldelsen). F1 resultat er en god metode til at opsummere beregning i et enkelt tal, men det er altid en god ide at kigge på både præcision og tilbagekaldelsen sammen for bedre at forstå, hvordan en klassificering reagere.

Desuden kan en Undersøg SAND positive sats kontra falsk positiv rente i kurven **Modtager operativsystem egenskab (ROC)** og den tilsvarende værdi i **Området Under feltet kurve (AUC)** . Jo nærmere kurven er til det øverste venstre hjørne, desto bedre den klassificering ydeevne er (, der maksimere SAND positive sats og minimere den false positiv rente). Kurver, der er tæt diagonalt af afbildning, resultatet fra klassificeringer, der er tendens til at forudsige, der er tæt tilfældige gætte.

###<a name="using-cross-validation"></a>Ved hjælp af på tværs af datavalidering###
Vi kan udføre cross validering for at gange oplære karakteren og evaluerer forskellige underliggende data automatisk som eksemplet regression. På samme måde, kan vi bruge [Tværs validere Model] [ cross-validate-model] modul, en baseret logistisk regressionsmodel og et datasæt. Kolonnen navn skal være indstillet til *indtægter* i [Tværs validere Model] [ cross-validate-model] modulet egenskaber. Når du kører forsøget og klikke på den højre outputport i [Tværs validere Model] [ cross-validate-model] modulet, kan vi se binære klassifikation metriske værdier for hver højformat desuden til middelværdi og standardafvigelse for hver. 
 
![Kryds-validere en Model til binær klassifikation](media/machine-learning-evaluate-model-performance/8.png)

Figur 8. Kryds-validere en Model til binær klassificering.

![Resultater i tværs-validering af en binær klassificering](media/machine-learning-evaluate-model-performance/9.png)

Figur 9. Tværs validering resultaterne af en binær klassificering.

##<a name="evaluating-a-multiclass-classification-model"></a>Evaluering af en Multiclass klassifikation Model##
I dette forsøg vil vi bruge populære [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") datasættet som indeholder forekomster af 3 forskellige typer (klasser) iris plante. Der er 4 funktion-værdier (sepal længde og bredde og bladet længde og bredde) for hver forekomst. I de forrige forsøg vi uddannelse og testet modeller ved hjælp af den samme datasæt. Her kan vi vil bruge de [Opdelte Data] [ split] modul til at oprette 2 undersæt af data, undervise på først og resultat og evaluerer på andet. Iris datasættet er offentligt tilgængelig på [UCI Machine Learning lager](http://archive.ics.uci.edu/ml/index.html)og kan hentes ved hjælp af en [Importdata] [ import-data] modul.

###<a name="creating-the-experiment"></a>Oprette forsøget###
Tilføje følgende moduler til dit arbejdsområde i Azure Machine Learning Studio:

- [Importdata][import-data]
- [Multiclass beslutning skov][multiclass-decision-forest]
- [Opdele Data][split]
- [Tog Model][train-model]
- [Resultat Model][score-model]
- [Evaluere Model][evaluate-model]

Forbind portene, som vist nedenfor i figur 10.

Angive kolonneindeks etiket [Tog Model] [ train-model] modul til 5. Datasættet har ingen kolonneoverskrift, men vi vide, at klassen etiketterne femte kolonne.

Klik på [Importdata] [ import-data] modul og angive egenskaben *datakilde* til *URL-adresse via HTTP*og *URL-adressen* til http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Angiv del af forekomster, der skal bruges til uddannelse i de [Opdelte Data] [ split] modul (0.7 for eksempel).
 
![Evaluering af en Multiclass klassificering](media/machine-learning-evaluate-model-performance/10.png)

Figur 10. Evaluering af en Multiclass klassificering

###<a name="inspecting-the-evaluation-results"></a>Undersøge evalueringsresultaterne###
Køre forsøget, og klik på outputporten af [Evaluere Model][evaluate-model]. Evalueringsresultaterne vises i dette tilfælde i form af en forvirring matrix. Matrixen viser den faktiske kontra estimerede forekomster for alle 3 klasser.
 
![Multiclass klassificering evalueringsresultater](media/machine-learning-evaluate-model-performance/11.png)

Figur 11. Multiclass klassificering evalueringsresultater.

###<a name="using-cross-validation"></a>Ved hjælp af på tværs af datavalidering###
Som nævnt tidligere, du kan udføre gentagne undervisning, vundne og evaluering automatisk ved hjælp af [Tværs validere Model] [ cross-validate-model] modul. Du har brug for et datasæt, en baseret model og en [Kryds-validere Model] [ cross-validate-model] modul (se figuren nedenfor). Igen skal du oprette kolonnen navn i [Tværs validere Model] [ cross-validate-model] modul (kolonneindeks 5 i dette tilfælde). Når kører forsøget og klikke på højre output port i [Tværs validere Model][cross-validate-model], kan du undersøge de metriske værdier for hver højformat samt den gennemsnitlige og standardversionen afvigelse. Oplysningerne vises her angives den ligner dem, der er beskrevet i binær klassifikation store og små bogstaver. Bemærk, at dog i multiclass klassifikation, computing SAND positive/negativer og falsk positive/negativer er udført af optælling på grundlag klasse, som der findes ingen overordnede positivt eller negativt klasse. For eksempel ved beregning af præcision eller tilbagekaldelse af klassen Iris-setosa, antages det, at dette er den positive klasse og alle andre som negativt.
 
![Validere en Model Multiclass klassifikation tværs](media/machine-learning-evaluate-model-performance/12.png)

Figur 12. Kryds-validere en Model Multiclass klassificering.


![Tværs validering resultaterne af en Multiclass klassifikation Model](media/machine-learning-evaluate-model-performance/13.png)

Figur 13. Tværs validering resultaterne af en Multiclass klassifikation Model.


<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
 
