<properties 
    pageTitle="Udføre Python machine learning scripts | Microsoft Azure" 
    description="Dispositioner designe principper underliggende understøttelse af Python scripts i Azure Machine Learning og grundlæggende brugsscenarier, funktioner og begrænsninger." 
    keywords="Python maskine læ, pandas, python pandas, python scripts, udføre python scripts"
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


# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Udføre Python machine learning scripts i Azure maskine læ Studio

Dette emne beskrives de underliggende den aktuelle understøttelse af Python scripts i Azure Machine Learning principper for design. De vigtigste funktioner også fremhæves, herunder support til at importere eksisterende kode, eksportere visualiseringer, og til sidst skal nogle af de begrænsninger og igangværende arbejde gennemgås.

[Python](https://www.python.org/) er et nødvendigt værktøj i værktøjet brysthulen for mange data videnskabelige eksperter. Det har:

-  en elegant og kortfattet syntaks 
-  understøttelse af flere platforme 
-  en omfattende samling af effektive biblioteker, og 
-  forfaldne værktøjer til udvikling af. 

Python der bruges i alle faser af arbejdsprocessen for typisk bruges i machine learning modellering, fra data indtager og behandling til funktion byggeri og model uddannelse, og klik derefter validering og installation af modellerne. 

Azure maskine læ Studio understøtter integrering Python scripts til forskellige dele af en maskine læ forsøg og også problemfrit at publicere dem som SVG, opgivet webtjenester på Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Designe principper for Python scripts i Machine Learning
Den primære brugergrænseflade til Python i Azure Machine Learning Studio er via [Udføre Python Script] [ execute-python-script] modul, der er vist i figur 1.

![image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Figur 1. **Udføre Python Script** -modul.

[Udføre Python Script] [ execute-python-script] modul accepterer op til tre input og producerer op til to output (beskrevet nedenfor), ligesom dens R analog, [Udføre R Script] [ execute-r-script] modul. Python koden, der skal udføres er angivet i feltet parameter som et særligt navngivet indgangspunkt funktionen kaldet `azureml_main`. Her er de vigtigste designprincipper, der bruges til at implementere dette modul:

1.  *Være bestemte for Python brugere.* De fleste Python brugere angive deres kode som funktioner i moduler, så hvordan man anbringer en masse eksekverbare sætninger i et websted på øverste modul er relativt sjældne. Feltet script tager som et resultat, et særligt navngivet Python-funktionen i modsætning til blot en række sætninger. De objekter, der vises i funktionen er standard Python bibliotek typer som [Pandas](http://pandas.pydata.org/) datarammer og [NumPy](http://www.numpy.org/) matrixer.
2.  *Skal have høj pålidelighed mellem lokale og skybaserede udførelser.* Backend-version, bruges til at udføre Python koden er baseret på [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, en udbredte på tværs af platforme videnskabelig Python fordeling. Den leveres med tæt 200 af de mest almindelige Python pakker. Derfor kan data forskere fejlfinding og kvalificeret deres kode på deres lokale Azure Machine Learning-kompatible Anaconda-miljø. Brug derefter eksisterende udviklingsmiljøer som [IPython](http://ipython.org/) notesbog eller [Python Tools til Visual Studio](http://aka.ms/ptvs) til at køre den som en del af et Azure Machine Learning forsøg med høj sikkerhed. Yderligere, den `azureml_main` indgangspunkt er en vanille Python funktion og kan være skrevet uden Azure Machine Learning bestemt kode eller SDK installeret.
3.  *Være problemfrit composable med andre Azure Machine Learning moduler.* [Udføre Python Script] [ execute-python-script] modul accepterer, som input og output, standard Azure Machine Learning datasæt. Den underliggende framework funktioner transparent og effektivt på Azure maskine læring og Python kørsel af (understøtter funktioner som manglende værdier). Python kan derfor bruges sammen med eksisterende Azure Machine Learning arbejdsprocesser, herunder dem, der ringer til R og SQLite. En kan derfor agter arbejdsprocesser, som:
  * Brug af Python og Pandas til forbehandling af og rydning af unødvendig, 
  * feed dataene til en SQL-transformation, deltage i flere datasæt til formularfunktioner 
  * oplære modellerne ved hjælp af den omfattende samling af algoritmer i Azure maskine undervisning og 
  * evaluere og efter behandle resultater ved hjælp af R.


## <a name="basic-usage-scenarios-in-machine-learning-for-python-scripts"></a>Grundlæggende brugsscenarier i Machine Learning for Python scripts
I dette afsnit skal vi undersøgelse med nogle af de grundlæggende anvendelser af [Udføre Python Script] [ execute-python-script] modul.
Som tidligere nævnt er en hvilken som helst input til modulet Python vises som Pandas datarammer. Yderligere oplysninger om Python Pandas og hvordan de kan bruges til at manipulere data effektivt og effektivt kan findes i *Python for dataanalyse* (O'Reilly, 2012) ved Vesteuropa McKinney. Funktionen skal returnere en enkelt Pandas data ramme pakket inde i en Python [sekvens](https://docs.python.org/2/c-api/sequence.html) som en tupel, en liste eller et NumPy matrix. Det første element i denne rækkefølge returneres derefter i den første outputport i modulet. Dette skema er vist i figur 2.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figur 2. Tilknytning af input porte til parametre og returværdi til outputport.

Mere detaljerede semantik af hvordan de input porte knyttet til parametrene for den `azureml_main` funktionen vises i tabel 1:

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabel 1. Tilknytning af input porte til funktionsparametre.

Tilknytningen mellem input-porte og funktionsparametre er position. Den første forbundne input port er knyttet til den første parameter af funktionen, og den anden input (hvis forbindelse) er knyttet til den anden parameter af funktionen.

## <a name="translation-of-input-and-output-types"></a>Oversættelse af input- og outputområder typer
Som beskrevet tidligere, konverteres input datasæt i Azure Machine Learning til data rammer i Pandas og output datarammer konverteres tilbage til Azure Machine Learning datasæt. De følgende konverteringer udføres:

1.  Strenge og numeriske kolonner er konverteret som-er og manglende værdier i et datasæt, konverteres til 'Ikke.tilgængelig' værdier i Pandas. Samme konverteringen sker på vej tilbage (NA værdier i Pandas konverteres til manglende værdier i Azure Machine Learning).
2.  Indeks vektorer i Pandas understøttes ikke i Azure Machine Learning. Alle inputdataene rammer i funktionen Python har altid et 64-bit numeriske indeks mellem 0 og antallet af rækker minus 1. 
3.  Azure Machine Learning datasæt kan ikke have duplikerede kolonnenavne og kolonnenavne, der ikke er strenge. Hvis en output data ramme indeholder ikke-numeriske kolonner, rammerne kalder `str` fra kolonnenavne. På samme måde en hvilken som helst duplikerede kolonnenavne er automatisk ødelagt for at sikre, at navnene er entydige. Suffiks (2) føjes til den første dublet, (3) til den anden dublet, osv.

## <a name="operationalizing-python-scripts"></a>Operationalizing Python scripts
En hvilken som helst [Udføre Python Script] [ execute-python-script] moduler, der bruges i en vurdering forsøg kaldes, når de udgives som en webtjeneste. Figur 3 vises eksempelvis en vurdering forsøg, der indeholder koden for at evaluere et enkelt Python udtryk. 

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Figur 3. Webtjeneste til at vurdere en Python udtryk.

En webtjeneste, der er oprettet ud fra dette forsøg benytter som input et Python udtryk (som en streng), sender den til Python fortolker og returnerer en tabel, der indeholder både udtrykket og evalueret resultatet.

## <a name="importing-existing-python-script-modules"></a>Importere eksisterende Python script moduler
En fælles use case for mange data forskere er at inkorporere eksisterende Python scripts i Azure Machine Learning forsøg. I stedet for at sammenkæde og indsætte al kode i en enkelt script boks, [Udføre Python Script] [ execute-python-script] modul accepterer en tredje input port, der kan forbindes en zip-fil, der indeholder Python modulerne. Filen er derefter pakket ved udførelse af rammerne på kørselstidspunktet, og indholdet føjes til biblioteket stien til Python fortolker. Den `azureml_main` indgangspunkt funktionen kan derefter importere disse moduler direkte.

Overvej filen Hello.py, der indeholder en enkelt "Hej, store verden" funktion som et eksempel.

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

Figur 4. Brugerdefineret funktion.

Dernæst skal oprette vi en fil Hello.zip, der indeholder Hello.py:

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

Figur 5. Zip-fil, der indeholder brugerdefinerede Python kode.

Derefter overføre dette som et datasæt til Azure Machine Learning Studio. Oprette og køre en simpel forsøg, der anvender Python kode i filen Hello.zip ved at knytte den til den tredje input port af Python udføre Script, som vist i denne illustration.

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

Figur 6. Eksempel eksperimentere med brugerdefinerede Python kode, der er overført som en zip-fil.

Modulet output viser, at zip-filen er blevet uemballerede og funktionen `print_hello` faktisk er kørt.
 
![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
Figur 7. Brugerdefineret funktion i brug i [Udføre Python Script] [ execute-python-script] modul.

## <a name="working-with-visualizations"></a>Arbejde med visuelle effekter
Områder, der er oprettet ved hjælp af MatplotLib, der kan visualiseres på browseren, kan der returneres ved at [Udføre Python Script][execute-python-script]. Men: omdirigeres ikke automatisk til billeder, som de er, når du bruger R. Så skal brugeren eksplicit gemme alle områder på PNG-filer hvis de skal returneres tilbage til Azure Machine Learning. 

For at oprette billeder fra MatplotLib, skal du konkurrere følgende fremgangsmåde:

* skifte backend-version til "AGG" fra standard Qt-baserede gengivelse 
* oprette et nyt figur objekt 
* få aksen, og generere alle områder i den 
* gemme figuren i en PNG-fil 

Denne proces illustreres i den følgende figur 8, som opretter en punktdiagrammer afbildning matrix, der bruger funktionen scatter_matrix i Pandas.
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Figur 8. Gemme MatplotLib figurer til billeder.



Figur 9 viser et forsøg, der bruger scriptet vises tidligere til at returnere plottes via den anden outputport.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png) 
     
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png) 

Figur 9. Visualisere områder, der er dannet ud fra Python kode.

Det er muligt at returnere flere figurer ved at gemme dem til forskellige billeder, Azure Machine Learning runtime henter alle billeder og sammenkæder dem til visualisering.


## <a name="advanced-examples"></a>Avancerede eksempler
Anaconda miljøet installeret i Azure maskine læ indeholder almindelige pakker, som NumPy, SciPy, og lær Scikits, og disse kan bruges effektivt til forskellige databehandling opgaver i en typisk machine learning pipeline. Som eksempel illustrerer følgende forsøgene og script brugen af ensemble studerende i Scikits Lær at beregne funktion prioritet pointtal for et datasæt. Resultaterne kan derefter bruges til at udføre tilsyn funktion markering før fødning af i en anden maskine learning model.

Funktionen Python til at beregne prioritet pointtal og rækkefølge, de funktioner, der er baseret på den er vist nedenfor:

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

Figur 10. Funktionen til plads funktioner ved resultater.
 Følgende forsøget derefter beregner og returnerer prioritet resultater af funktioner i "Pima indiske Diabetes" datasættet i Azure maskine undervisning:

![image12](./media/machine-learning-execute-python-scripts/figure9a.png)
![image13](./media/machine-learning-execute-python-scripts/figure9b.png)    
    
Figur 11. Eksperimentere til plads funktioner i Pima indiske Diabetes datasættet.

## <a name="limitations"></a>Begrænsninger 
[Udføre Python Script] [ execute-python-script] aktuelt har følgende begrænsninger:

1.  *Sandkassetilstand udførelse.* Python runtime er i øjeblikket sandkassetilstand og derfor tillader ikke adgang til netværket eller til det lokale filsystem på en fast måde. Alle filer, der er gemt lokalt isolerede og slettet, når modulet, der afsluttes. Python koden kan ikke få adgang til de fleste mapper på den computer, der køres på de undtagelser, der den aktuelle mappe og dens undermapper.
2.  *Manglende avancerede udvikling og fejlfinding support.* Modulet Python understøtter i øjeblikket ikke ide-funktioner som intellisense og fejlfinding. Også, hvis modulet mislykkes på kørselstidspunktet, fuld Python staksporing er tilgængelig, men skal vises som outputlogfilen for modulet. Vi anbefaler, at udvikle og foretage fejlfinding af deres Python scripts i et miljø som IPython og derefter importere koden i modulet i øjeblikket.
3.  *Enkelt data ramme output.* Indgangspunkt Python er kun tilladt til at returnere en enkelt data ramme som output. Det er ikke i øjeblikket muligt at vende tilbage vilkårlig Python objekter som erfaren modeller direkte tilbage til Azure Machine Learning runtime. Sådan [Udføre R Script][execute-r-script], som har samme begrænsning, det er dog muligt i mange tilfælde til pickle objekter i en bytematrix, og derefter ENTER pågældende inde i en ramme data.
4.  *Manglende evne til at tilpasse Python installation*. I øjeblikket, er den eneste måde at tilføje brugerdefinerede Python moduler via zip-fil ordning beskrevet tidligere. Det er muligt for små moduler, er det besværligt for store moduler (især dem med oprindelige DLL-filer) eller et stort antal moduler. 


##<a name="conclusions"></a>Konklusioner
[Udføre Python Script] [ execute-python-script] modul gør det muligt for en data scientist at inkorporere eksisterende Python kode i skyen hostet machine learning arbejdsprocesser i Azure maskine undervisning og giver problemfrit muligheder for dem som en del af en webtjeneste. Modulet Python script naturligt samarbejder med andre moduler i Azure maskine læring og kan bruges til et område af opgaver fra undersøgelse af data til forbehandling til udtrækning af funktion, evaluering og efterbehandling af resultaterne. Bruges til udførelse af til kørsel af back end-er baseret på Anaconda, en grundigt testet og udbredte Python fordeling. Det gør det nemt for dig at indbyggede eksisterende kode aktiver i skyen.

Vi forventer, at levere ekstra funktioner til [Udføre Python Script] [ execute-python-script] modul som muligheden for at undervise og giver muligheder for datamodeller i Python og for at tilføje bedre understøttelse af udvikling og fejlfinding af kode i Azure Machine Learning Studio.

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Python Developer Center](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
