<properties
    pageTitle="Funktionen teknisk og markering i Azure Machine Learning | Microsoft Azure"
    description="Forklarer med henblik på valg af funktion og funktion teknisk og indeholder eksempler på deres rolle i den udvidede data processen med at machine learning."
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
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Funktionen teknisk og markering i Azure Machine Learning

Dette emne forklares det, med henblik på funktion teknisk og valg af funktion i processen udvidede data af machine learning. Det viser, hvad disse processer involverer ved hjælp af eksempler, der leveres af Azure Machine Learning Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Kursus dataene bruges i machine learning kan ofte forbedres ved markering eller udtrækning af funktioner fra rækkedata indsamles. Et eksempel på en engineering funktion i forbindelse med at lære at klassificere billeder af håndskrevne tegn er et tætheden af bit kort, der er dannet ud fra rå bit fordeling dataene. Denne tilknytning kan hjælpe dig med at finde tegnene kanter mere effektivt end den rå fordeling.

Engineering og markerede funktioner øget effektivitet af processen kursus, som forsøger at udtrække de vigtige oplysninger, der er indeholdt i dataene. De også forbedre en potens af disse modeller til at klassificere inputdataene præcist og til at forudsige resultater af interesse mere robustly. Funktionen teknisk og markeringen kan også kombinere for at gøre læring mere beregningsmæssigt tractable. Sker det ved at forbedre og derefter reducere antallet af funktioner, der er behov for at kalibrere eller uddannelse af en model. Matematisk taler er de funktioner, der er markeret til at undervise modellen et minimum af uafhængige variabler, der beskriver mønstrene i dataene og forudsige resultater korrekt.

Teknisk samt udvalg af funktioner, der er en del af en større proces, der typisk består af fire trin:

* Dataindsamling
* Udvidede data
* Model byggeri
* Efter behandling

Teknisk og markeringen udgør trinnet data forbedring af machine learning. Tre aspekter af denne proces kan skelnes til vores formål:

* **Data forbehandling**: denne proces forsøger at sikre, at de indsamlede data er ren og ensartede. Den indeholder opgaver såsom integrering af flere datasæt, håndtering af manglende data, håndtering af uoverensstemmende data og konvertering af datatyper.
* **Funktionen teknisk**: denne proces forsøger at oprette yderligere relevante funktioner fra de eksisterende rå funktioner i dataene og for at øge skønnet power learning algoritmen med.
* **Valg af funktion**: denne proces markerer det vigtigste undersæt af oprindelige datafunktioner til at reducere dimensionerne for kursus problemet.

I dette emne beskrives kun funktion teknisk og funktion markering aspekter af data udvidede processen. Du kan finde flere oplysninger på trinnet data forbehandling se [forbehandling af data i Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## <a name="creating-features-from-your-data--feature-engineering"></a>Oprettelse af funktioner fra dine data – funktionen teknisk

Datatypen kursus består af en matrix bestående af eksempler (poster eller bemærkninger, der er gemt i rækker), hver især har en række funktioner (variabler eller felter, der er gemt i kolonner). De funktioner, der er angivet i udformningen forventes til at beskrive mønstrene i dataene. Selvom mange af felterne rækkedata kan direkte medtaget i den valgte funktion bruges til at undervise en model, skal yderligere engineering funktioner ofte bestå af funktionerne i rækkedata til at oprette en udvidet kursus datasæt.

Hvilken type af funktioner, der skal oprettes til at forbedre datasættet, når kursus en model? Engineering funktioner, der forbedrer uddannelse indeholder oplysninger, der adskiller bedre mønstre i dataene. Du forventer de nye funktioner til at angive yderligere oplysninger, der ikke registreres klart eller nemt synlige i den oprindelige eller eksisterende funktionssæt, men denne proces er noget af en illustration. Lyd og produktiv beslutninger kræver ofte nogle domæne ekspertise.

Når du starter med Azure Machine Learning, er det nemmest at forstå denne proces concretely ved hjælp af eksemplerne i Machine Learning Studio. To eksempler præsenteres her:

* Et eksempel regression ([forudsigelse af antallet cykler husleje](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) i en kontrolleret forsøg, hvor det er blevet konstateret på målværdier
* En tekst-mining klassifikation eksempel på brug af [Funktionen krypteres][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Eksempel 1: Tilføje tidsmæssig funktioner for en regressionsmodel ###

Hvis du vil vise, hvordan du engineering funktioner til en regression opgave, vi bruge den forsøg "Demand prognoser af cykler" i Azure Machine Learning Studio. Formålet med dette forsøg er at forudsige behovet for cykler, det vil sige, antallet af cykler husleje inden for en bestemt måned, dag eller time. Datasættet **cykler leje UCI stokastisk variabel** bruges som input rækkedata.

Dette datasæt er baseret på reelle data fra kapital Bikeshare virksomhed, der fører en cykler leje netværk i Washington DC i USA. Datasættet repræsenterer antallet af cykler husleje inden for en bestemt time af en dag fra 2011 til 2012, og den indeholder 17379 rækker og 17 kolonner. Rå funktion sættet indeholder vejrudsigt betingelser (temperatur, luftfugtighed, vindhastighed) og typen dagens (ferie eller ugedag). Feltet til at forudsige er **cnt**, en optælling, der repræsenterer cykler husleje inden for en bestemt time og, der går fra 1 til 977.

For at oprette effektive funktioner i kursus dataene, er fire regression modeller udviklet ved hjælp af den samme algoritme, men med fire forskellige kursus datasæt. De fire datasæt repræsenterer samme input rådata, men med en stigende række funktioner angive. Disse funktioner er inddelt i fire kategorier:

1. A = vejrudsigt + Feriekalender + ugedag + weekend funktioner for den estimerede dag
2. B = antallet af cykler, der blev lejes i hver af de forrige 12 timer
3. C = antallet af cykler, der blev lejes i hver af de forrige 12 dage på den samme time
4. D = antallet af cykler, der blev lejes i hver af de forrige 12 uger på den samme time og den samme ugedag

Ud over funktion sæt A, som allerede findes i den oprindelige rækkedata, oprettes de andre tre sæt af funktioner via funktionen engineering-processen. Funktionen angive B noter det seneste behov for cyklerne. Funktionen angive C noter krav om cykler på et bestemt tidspunkt. Funktionen indstillet D noter krav om cykler til bestemt time og bestemt dag i ugen. Hver af de fire kursus datasæt omfatter funktionssæt A, A + B, A + B + C og A + B + C + D, henholdsvis.

I forsøget Azure Machine Learning udgøres disse fire kursus datasæt via fire forgreninger fra før behandlede input datasættet. Med undtagelse af den længst til venstre gren, hver af disse forgreninger indeholder et [Udføre R Script] [ execute-r-script] modul, hvor et sæt afledt funktioner (funktionen angiver B, C og D) henholdsvis er opbygget og føjet til de importerede data. I følgende figur vises den R-script, der bruges til at oprette funktionssæt B i den anden venstre gren.

![Oprette en funktionssæt](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

Den følgende tabel opsummerer sammenligning af resultater af de fire modeller. De bedste resultater vises efter funktioner A + B + C. Bemærk, at fejl rente falder med, når yderligere funktionssæt er inkluderet i kursus dataene. Dette kontrollerer vores mistanke, funktionssæt B og C angive yderligere relevante oplysninger om den simple lineære regression opgave. Tilføje funktionssæt D tilsyneladende ikke til at levere eventuelle yderligere reduktion i fejl rente.

![Sammenligne resultater](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a>Eksempel 2: Oprette funktioner i tekst mining  

Funktionen teknisk anvendes bredt i opgaver, der er relateret til tekst mining, som dokumentet klassificering og synspunkt analyse. Når du vil klassificere dokumenter i flere kategorier, er en typisk antagelse f.eks, at de ord eller sætninger, der er inkluderet i ét dokumentkategori er mindre kan opstå i et andet dokumentkategori. Med andre ord er hyppigheden for ord eller udtryk stokastiske i stand til at beskrive andet dokumentkategorier. Funktionen engineering-processen i tekst mining programmer, er behov for at oprette de funktioner, der involverer ordet eller sætningen hyppighed, da individuelle elementer af tekst-indhold som regel fungerer som inputdataene.

For at opnå denne opgave skal anvendes en teknik kaldet *funktion krypteres* for at slå effektivt vilkårlig tekstfunktioner i indeks. I stedet for at tilknytte hver tekst funktion (ord eller sætninger) til et bestemt indeks, denne metode funktioner ved at anvende en til funktionerne og ved hjælp af deres hashværdier som indeks direkte.

I Azure Machine Learning, er der ikke en [Funktion krypteres] [ feature-hashing] modul, der opretter funktionerne ord eller udtryk. I følgende figur vises et eksempel på brug af dette modul. Indtast datasættet ikke indeholder to kolonner: den adressekartotek bedømmelse, lige fra 1 til 5 og de faktiske Gennemse indhold. Formålet med denne [Funktion krypteres] [ feature-hashing] modul er at hente nye funktioner, der viser forekomst hyppigheden for de tilsvarende ord eller sætninger i bestemt adressekartotek Gennemse. Hvis du vil bruge dette modul, skal du benytte følgende fremgangsmåde:

1. Vælg den kolonne, der indeholder den input tekst (**Kol2** i dette eksempel).
2. Angiv *Hashing bitsize* til 8, hvilket betyder, at 2 ^ 8 = 256 funktioner, der er oprettet. Ordet eller sætningen i feltet tekst, derefter hashes til 256 indeks. Parameteren *Hashing bitsize* går fra 1 til 31. Hvis parameteren er indstillet til et højere tal, er de ord eller sætninger mindre sandsynligvis hash'es i det samme indeks.
3. Angive parameteren *N-g* til 2. Dette henter forekomst hyppigheden for unigrams (en funktion af hver enkelt ord) og bigrams (en funktion for hvert par af tilstødende ord) fra Skriv tekst. Parameteren *N-g* går fra 0 til 10, som angiver det maksimale antal sekventielle ord, der skal medtages i en funktion.  

![Funktionen hashing-modul](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

I følgende figur vises, hvordan disse nye funktioner ud.

![Funktionen hashing-eksempel](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Filtreringsfunktioner fra dine data – valg af funktion  ##

*Valg af funktion* er en proces, der ofte anvendes til opbygning af kursus datasæt for skønnet modellering opgaver såsom klassifikation eller regression opgaver. Målet er at vælge et undersæt af funktionerne i det oprindelige datasæt, der reducerer dets mål ved hjælp af et minimum af funktioner til at repræsentere den maksimale mængde variansen i dataene. Denne undersæt af funktioner, der indeholder funktioner, der kun medtages for at træne modellen. Valg af funktion tjener to primære formål:

* Valg af funktion øger ofte klassifikation nøjagtigheden ved at fjerne irrelevante eller overflødige eller nøje sammen funktioner.
* Valg af funktion reducerer antallet af funktioner, hvilket gør model kursus processen mere effektiv. Dette er særligt vigtig for studerende, der er dyr til at undervise som support vektor computere.

Selvom valg af funktion har til formål at reducere antallet af funktioner i datasættet bruges til at undervise modellen, det er ikke normalt henvises til i ordet *dimensionalitet reduktion.* Funktionen markeringsmetoder udtrække et undersæt af oprindelige funktioner i dataene uden at ændre dem.  Dimensionalitet reduktion metoder anvende engineering funktioner, der kan transformere de oprindelige funktioner og rediger dem, og som derfor. Vigtigste komponenter analyse, vedtaget korrelations analyse og enkelt værdi opdelingstræ er eksempler på dimensionalitet reduktion metoder.

Et almindeligt anvendte kategori af funktion markeringsmetoder i en kontrolleret kontekst er valg af filter-baserede funktion. Ved at evaluere korrelation mellem hver funktion og den destinationsadresse-attribut, anvende disse metoder en statistisk måling for at tildele en bogstavkarakter til hver funktion. Funktionerne, derefter klassificeres ud fra resultatet, så du kan bruge til at angive grænsen for bevarelse af eller fjerne en bestemt funktion. Pearson korrelations, fælles oplysninger og chi2-testen er eksempler på de statistiske målinger, der bruges i disse metoder.

Azure Machine Learning Studio indeholder moduler til valg af funktion. Som vist i følgende figur, disse moduler omfatter [Filter-baserede valg af funktion] [ filter-based-feature-selection] og [Fisher lineær Discriminant analyse][fisher-linear-discriminant-analysis].

![Funktionen markering eksempel](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


For eksempel bruge [Filter-baserede valg af funktion] [ filter-based-feature-selection] modul med teksteksempel mining beskrevet tidligere. Forudsætter, at du vil oprette en regressionsmodel, når et sæt 256 funktioner er oprettet via [Funktionen krypteres] [ feature-hashing] modul, og at variablen svar er **Kol1** og repræsenterer en bog Gennemse bedømmelse lige fra 1 til 5. Angiv **funktion vundne metode** til **Pearson korrelations**, **målkolonnen** til **Kol1**og **antallet af ønskede funktioner** til **50**. Modulet [Filter-baserede valg af funktion] [ filter-based-feature-selection] derefter giver et datasæt, der indeholder 50 funktioner sammen med attributten target **Kol1**. I følgende figur vises strømmen af dette forsøg og de inputparametre.

![Funktionen markering eksempel](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

I følgende figur vises de resulterende datasæt. Hver funktion er karakter baseret på Pearson korrelation mellem selve og attributten target **Kol1**. Funktioner med øverste resultater, bevares.

![Filter-baserede funktion markering datasæt](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

I følgende figur vises de tilsvarende resultater af de markerede funktioner.

![Valgte funktion i læsbarhedstest](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Ved at anvende denne [Filter-baserede valg af funktion] [ filter-based-feature-selection] modul, 50 ud af 256 funktioner er valgt, fordi de har mest funktioner mellem med variable **Kol1** baseret på metoden vurdering **Pearson korrelations**destinationen.

## <a name="conclusion"></a>Konklusion
Funktionen teknisk og valg af funktion er to trin udføres ofte for at forberede kursus data, når du opbygger en machine learning model. Normalt funktion teknisk anvendes først for at generere yderligere funktioner, og derefter funktion markering trin udføres for at udelukke irrelevante, overflødige eller meget forbundne Funktioner.

Det er ikke altid behøver at udføre valg af funktion teknisk eller en funktion. Om det er nødvendigt, afhænger af de data, du har eller indsamling, algoritmen du vælge og formålet med forsøget.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
