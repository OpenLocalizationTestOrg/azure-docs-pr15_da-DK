<properties
    pageTitle="Funktionen teknisk i processen Cortana Analytics | Microsoft Azure" 
    description="Forklarer formålet med funktionen teknisk og indeholder eksempler på dens rolle i data udvidede processen med at machine learning."
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
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-in-the-cortana-analytics-process"></a>Funktionen teknisk i processen Cortana Analytics 

Dette emne forklares, med henblik på funktion teknisk og indeholder eksempler på dens rolle i data udvidede processen med at machine learning. Eksempler på bruges til at illustrere denne proces trækkes fra Azure Machine Learning Studio. 

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

I denne **menu** links til emner, der beskriver, hvordan du opretter funktioner til data i forskellige miljøer. Denne opgave er et trin i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Funktionen engineering forsøg på at øge skønnet fordelene læ algoritmer ved at oprette funktioner fra rækkedata, lettere learning processen. Teknisk samt udvalg af funktioner, der er en del af den TDSP, der er beskrevet i de [Hvad er den proces, Team Data videnskabelige?](data-science-process-overview.md) Funktionen teknisk og markering er en del af trinnet **udvikling funktioner** i TDSP. 

* **funktionen teknisk**: denne proces forsøger at oprette yderligere relevante funktioner fra de eksisterende rå funktioner i dataene, og for at øge skønnet power af algoritmen learning.

* **valg af funktion**: denne proces markerer det vigtigste undersæt af funktioner til oprindelige data i et forsøg på at reducere dimensionerne for kursus problemet.

**Funktion teknisk** anvendes normalt først for at generere yderligere funktioner, og derefter **vælge funktioner** trin udføres for at udelukke irrelevante, overflødige eller meget forbundne Funktioner.

Kursus dataene bruges i machine learning kan ofte forbedres ved udtrækning af funktioner fra rækkedata indsamles. Et eksempel på en engineering funktion i forbindelse med at lære at klassificere billeder af håndskrevne tegn er oprettelse af en smule tætheden af kort opbygget fra rå bit fordeling data. Denne tilknytning kan hjælpe dig med at finde tegnene kanter mere effektivt end blot ved hjælp af den rå fordeling direkte.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="creating-features-from-your-data---feature-engineering"></a>Oprettelse af funktioner fra dine Data - funktionen teknisk

Datatypen kursus består af en matrix bestående af eksempler (poster eller bemærkninger, der er gemt i rækker), hver især har en række funktioner (variabler eller felter, der er gemt i kolonner). De funktioner, der er angivet i udformningen forventes til at beskrive mønstrene i dataene. Selvom mange af felterne rækkedata kan direkte medtaget i den valgte funktion bruges til at undervise en model, er det ofte tilfældet, yderligere (engineering) funktioner skal bestå af funktionerne i rækkedata til at oprette en udvidet kursus datasæt.

Hvilken type af funktioner, der skal oprettes til at forbedre datasættet, når kursus en model? Engineering funktioner, der forbedrer uddannelse indeholder oplysninger, der adskiller bedre mønstre i dataene. Vi forventer, at de nye funktioner til at angive yderligere oplysninger, der ikke er klart hentede eller nemt synlige i den oprindelige eller eksisterende funktionssæt. Men denne proces er noget af en illustration. Lyd og produktiv beslutninger kræver ofte nogle domæne ekspertise.

Når du starter med Azure Machine Learning, er det nemmest at forstå denne proces, concretely ved hjælp af eksemplerne i Studio. To eksempler præsenteres her:

* Et eksempel regression [forudsigelse af antallet cykler husleje](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) i en kontrolleret forsøg, hvor det er blevet konstateret på målværdier
* En tekst mining klassifikation eksempel på brug af [Funktionen krypteres](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### <a name="example-1-adding-temporal-features-for-regression-model"></a>Eksempel 1: Tilføje tidsmæssig funktioner for regressionsmodel ###

Lad os bruge den forsøg "Demand prognoser af cykler" i Azure Machine Learning Studio til at vise, hvordan du engineering funktioner til en regression opgave. Formålet med dette forsøg er at forudsige behovet for cykler, det vil sige, antallet af cykler husleje inden for en bestemt måned/dag/time. Datasættet "cykler leje UCI datasæt" bruges som input rækkedata. Dette dataset er baseret på reelle data fra kapital Bikeshare virksomhed, der fører en cykler leje netværk i Washington DC i USA. Datasættet repræsenterer antallet af cykler husleje inden for en bestemt time af en dag i år 2011 og år 2012 og indeholder 17379 rækker og 17 kolonner. Rå funktion sættet indeholder vejrudsigt betingelser (temperaturen/luftfugtighed/vindhastighed) og typen dagens (ferie/ugedag). Feltet til at forudsige er "cnt", en optælling, der repræsenterer cykler husleje inden for en bestemt time, og som områder, der går fra 1 til 977.

Med formålet med at oprette effektive funktioner i kursus dataene, fire regression modeller oprettes ved hjælp af den samme algoritme, men med fire forskellige kursus datasæt. De fire datasæt repræsenterer samme input rådata, men med en stigende række funktioner angive. Disse funktioner er inddelt i fire kategorier:

1. A = vejrudsigt + Feriekalender + ugedag + weekend funktioner for den estimerede dag
2. B = antallet af cykler, der blev lejes i hver af de forrige 12 timer
3. C = antallet af cykler, der blev lejes i hver af de forrige 12 dage på den samme time
4. D = antallet af cykler, der blev lejes i hver af de forrige 12 uger på den samme time og den samme ugedag

Ud over funktion sæt A, som allerede findes i den oprindelige rækkedata, oprettes de andre tre sæt af funktioner via funktionen engineering-processen. Funktionen angive B noter meget seneste behov for cyklerne. Funktionen angive C noter krav om cykler på et bestemt tidspunkt. Funktionen indstillet D noter krav om cykler til bestemt time og bestemt dag i ugen. De fire kursus datasæt hver indeholder henholdsvis funktion sæt A, A + B, A + B + C og A + B + C + D.

I forsøget Azure Machine Learning udgøres disse fire kursus datasæt via fire forgreninger fra før behandlede input datasættet. Bortset fra de fleste gren, hver af disse forgreninger indeholder venstre er et [Execute R Script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) -modul, hvor et sæt afledt funktioner (funktionen angivet B, C og D) henholdsvis opbygget og føjet til de importerede datasæt. I følgende figur vises den R-script, der bruges til at oprette funktionssæt B i den anden venstre gren.

![oprette funktioner](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

Sammenligning af resultater af de fire modeller er opsummeret i følgende tabel. De bedste resultater vises efter funktioner A + B + C. Bemærk, at fejl rente reduceres hvornår yderligere funktionssæt er inkluderet i kursus dataene. Det bekræfter vores mistanke, funktionssæt B, C angive yderligere relevante oplysninger om den simple lineære regression opgave. Men tilføje funktionen D tilsyneladende ikke til at levere eventuelle yderligere reduktion i fejl rente.

![sammenligning af resultat](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a>Eksempel 2: Oprette funktioner i tekst Mining  

Funktionen teknisk anvendes bredt i opgaver, der er relateret til tekst mining, som dokumentet klassificering og synspunkt analyse. For eksempel når vi vil klassificere dokumenter i flere kategorier, antages en typisk det, de word/sætninger, der er inkluderet i ét dokument kategori er mindre kan opstå i et andet dokument kategori. I et andet ord er hyppigheden for den stokastiske variabel ord/udtryk i stand til at beskrive andet dokumentkategorier. Da individuelle elementer af tekst-indhold som regel fungerer som inputdataene, bruges funktionen engineering-processen i tekst mining programmer, til at oprette de funktioner, der involverer word/sætning hyppigheden.

For at opnå denne opgave skal anvendes en teknik kaldet **funktion krypteres** for at slå effektivt vilkårlig tekstfunktioner i indeks. I stedet for at tilknytte hver tekst funktion (ord/sætninger) til et bestemt indeks, denne metode funktioner ved at anvende en til funktionerne, og brug af deres hashværdier som indeks direkte.

Azure maskine mere, der er en [Funktion krypteres](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, der opretter disse ord/sætning funktioner nemt. Følgende figur vises et eksempel på brug af dette modul. Indtast datasættet indeholder to kolonner: den adressekartotek bedømmelse, lige fra 1 til 5, og faktisk Gennemse indholdet. Formålet med denne [Funktion krypteres](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul er at hente en masse nye funktioner, som viser forekomst hyppigheden for det eller de tilsvarende ord / sætninger i bestemt book Gennemse. Hvis du vil bruge dette modul, har vi brug at fuldføre følgende trin:

* Først skal du vælge den kolonne, der indeholder den tekst, input ("Kol2" i dette eksempel).
* Andet, Angiv "Hashing bitsize" til 8, hvilket betyder 2 ^ 8 = 256 funktioner oprettes. Word/fasen i al tekst kan hash'es til 256 indeks. Parameteren "Hashing bitsize" går fra 1 til 31. Ordet/ordene / sætninger, der er mindre sandsynligvis hash'es i det samme indeks, hvis det er et større tal.
* Tredje, angive parameteren "N-g" til 2. Dette henter forekomst hyppigheden for unigrams (en funktion af hver enkelt ord) og bigrams (en funktion for hvert par af tilstødende ord) fra Skriv tekst. Parameteren "N-g" går fra 0 til 10, der angiver det maksimale antal sekventielle ord, der skal medtages i en funktion.  

!["Funktionen Hashing" modul](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

I følgende figur vises hvad disse oversigt over nye funktioner udseende som.

!["Funktionen Hashing" eksempel](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## <a name="conclusion"></a>Konklusion

Engineering og markerede funktioner øget effektivitet af processen kursus som forsøger at udtrække de vigtige oplysninger, der er indeholdt i dataene. De også forbedre en potens af disse modeller til at klassificere inputdataene præcist og til at forudsige resultater af interesse mere robustly. Funktionen teknisk og markeringen kan også kombinere for at gøre læring mere beregningsmæssigt tractable. Sker det ved at forbedre og derefter reducere antallet af funktioner, der er behov for at kalibrere eller uddannelse af en model. Matematisk taler er de funktioner, der er markeret til at undervise modellen et minimum af uafhængige variabler, der beskriver mønstrene i dataene og forudsige resultater korrekt.

Bemærk, at det ikke altid behøver at udføre valg af funktion teknisk eller en funktion. Om det er nødvendigt eller ej, afhænger af de data, vi har eller indsamling, algoritmen vi vælge og formålet med forsøget.
 
