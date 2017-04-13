<properties
    pageTitle="Funktionen markering i gruppen Data videnskabelige processen | Microsoft Azure" 
    description="Forklarer formålet med valg af funktion og indeholder eksempler på deres rolle i processen data forbedring af machine learning."
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


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Valg af funktion i Team Data videnskabelige proces (TDSP)

I denne artikel forklarer formålet med valg af funktion og indeholder eksempler på dens rolle i data udvidede processen med at machine learning. Disse eksempler er tegnet fra Azure Machine Learning Studio. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Dette emne beskrives formålet med valg af funktion og indeholder eksempler på dens rolle i data udvidede processen med at machine learning. Disse eksempler er tegnet fra Azure Machine Learning Studio. 

Teknisk samt udvalg af funktioner, der er en del af den TDSP, der er beskrevet i de [Hvad er den proces, Team Data videnskabelige?](data-science-process-overview.md). Funktionen teknisk og markering er en del af trinnet **udvikling funktioner** i TDSP.

* **funktionen teknisk**: denne proces forsøger at oprette yderligere relevante funktioner fra de eksisterende rå funktioner i dataene, og for at øge skønnet power learning algoritmen med.

* **valg af funktion**: denne proces markerer det vigtigste undersæt af funktioner til oprindelige data i et forsøg på at reducere dimensionerne for kursus problemet.

**Funktion teknisk** anvendes normalt først for at generere yderligere funktioner, og derefter **vælge funktioner** trin udføres for at udelukke irrelevante, overflødige eller meget forbundne Funktioner.


## <a name="filtering-features-from-your-data---feature-selection"></a>Filtreringsfunktioner fra dine Data – valg af funktion 

Valg af funktion er en proces, der ofte anvendes til konstruktion af kursus datasæt for skønnet modellering opgaver såsom klassifikation eller regression opgaver. Målet er at vælge et undersæt af funktionerne i de oprindelige datasæt, reducere dets mål ved hjælp af et minimum af funktioner til at repræsentere den maksimale mængde variansen i dataene. Denne undersæt af funktioner er derefter kun funktioner til at indgå til at undervise modellen. Valg af funktion tjener to primære formål.

* Først skal valg af funktion øges ofte klassifikation nøjagtigheden ved at fjerne irrelevante eller overflødige eller nøje sammen funktioner.
* Andet, det reducerer antallet af funktioner, hvilket gør model kursus proces mere effektiv. Dette er særligt vigtig for studerende, der er dyr til at undervise som support vektor computere.

Selvom valg af funktion søger at reducere antallet af funktioner i datasættet bruges til at undervise modellen, den ikke som regel er omfattet af ordet "dimensionalitet reduktion". Funktionen markeringsmetoder udtrække et undersæt af oprindelige funktioner i dataene uden at ændre dem.  Dimensionalitet reduktion metoder anvende engineering funktioner, der kan transformere de oprindelige funktioner og rediger dem, og som derfor. Eksempler på dimensionalitet reduktion metoder omfatter hovedstolen komponent analyse, vedtaget korrelations analyse og enkelt værdi opdelingstræ.

Blandt andet kaldes en almindeligt anvendte kategori af funktion markeringsmetoder i en kontrolleret kontekst "baseret funktion filtervalg". Ved at evaluere korrelation mellem hver funktion og den destinationsadresse-attribut, anvende disse metoder en statistisk måling for at tildele en bogstavkarakter til hver funktion. Funktionerne, derefter klassificeres ud fra det resultat, som kan bruges til at angive grænsen for bevarelse af eller fjerne en bestemt funktion. Person korrelations, fælles oplysninger og Chi kvadrerede testen er eksempler på de statistiske målinger, der bruges i disse metoder.

Der findes i Azure Machine Learning Studio moduler, der er knyttet til valg af funktion. Som vist i følgende figur, disse moduler omfatter [Filter-baserede valg af funktion] [ filter-based-feature-selection] og [Fisher lineær Discriminant analyse][fisher-linear-discriminant-analysis].

![Funktionen markering eksempel](./media/machine-learning-data-science-select-features/feature-Selection.png)


Overveje, for eksempel brugen af [valg af Filter-baserede funktion] [ filter-based-feature-selection] modul. Med henblik på nemmere, vi fortsætte med at bruge teksteksempel mining beskrevet ovenfor. Lad os antage, at vi ønsker at opbygge en regressionsmodel, når et sæt 256 funktioner er oprettet via [Funktionen krypteres] [ feature-hashing] modul, og at variablen svar er "Kol1" og repræsenterer en bog Gennemse bedømmelser lige fra 1 til 5. Ved at angive "Funktionen vurdering metode" er "Pearson korrelations", "målkolonnen" skal være "Kol1" og "Antallet af ønskede funktioner" til 50. Derefter modul [Filter-baserede valg af funktion] [ filter-based-feature-selection] der resulterer i et datasæt, der indeholder 50 funktioner sammen med målattributten "Kol1". I følgende figur vises strømmen af dette forsøg og inputparametre vi blot beskrevet.

![Funktionen markering eksempel](./media/machine-learning-data-science-select-features/feature-Selection1.png)

I følgende figur vises den resulterende datasæt. Hver funktion er karakter baseret på Pearson korrelation mellem selve og målattributten "Kol1". Funktioner med øverste resultater, bevares.

![Funktionen markering eksempel](./media/machine-learning-data-science-select-features/feature-Selection2.png)

De tilsvarende resultater af de markerede funktioner er vist i følgende figur.

![Funktionen markering eksempel](./media/machine-learning-data-science-select-features/feature-Selection3.png)

Ved at anvende denne [Filter-baserede valg af funktion] [ filter-based-feature-selection] modul, 50 ud af 256 funktioner er valgt, fordi de har de mest forbundne funktioner med variablen mål "Kol1", baseret på metoden vurdering "Pearson korrelations".

## <a name="conclusion"></a>Konklusion
Funktionen teknisk og valg af funktion er to ofte engineering og valgte funktioner øget effektivitet af processen kursus som forsøg på at udtrække de vigtige oplysninger, der er indeholdt i dataene. De også forbedre en potens af disse modeller til at klassificere inputdataene præcist og til at forudsige resultater af interesse mere robustly. Funktionen teknisk og markeringen kan også kombinere for at gøre læring mere beregningsmæssigt tractable. Sker det ved at forbedre og derefter reducere antallet af funktioner, der er behov for at kalibrere eller uddannelse af en model. Matematisk taler er de funktioner, der er markeret til at undervise modellen et minimum af uafhængige variabler, der beskriver mønstrene i dataene og forudsige resultater korrekt.

Bemærk, at det ikke altid behøver at udføre valg af funktion teknisk eller en funktion. Om det er nødvendigt eller ej, afhænger af de data, vi har eller indsamling, algoritmen vi vælge og formålet med forsøget.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
