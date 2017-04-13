<properties
    pageTitle="Hvordan en Machine Learning model skrider frem fra et forsøg til en operationalized webtjeneste | Microsoft Azure"
    description="En oversigt over funktionen af hvordan dit Azure Machine Learning model skrider frem fra en udvikling eksperimentere til en operationalized webtjeneste indstillingen."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Hvordan en Machine Learning model skrider frem fra et forsøg til en operationalized webtjeneste

Azure Machine Learning Studio indeholder en interaktiv lærred, der gør det muligt at udvikle, køre, teste og forbedrer en ***eksperimentere*** der repræsenterer en skønnet analyse model. Der er en lang række moduler, der er tilgængelige, der kan:

* Indtast data til din forsøg
* Arbejde med dataene
* Uddannelse af en model, ved hjælp af machine learning algoritmer
* Resultat modellen
* Evaluere resultaterne
* Output endelige værdier

Når du er tilfreds med din forsøg, kan du installere det som en ***Klassisk Azure Machine Learning-webtjeneste*** eller en ***Ny Azure Machine Learning-webtjeneste*** , så brugerne kan sende den nye data og modtage tilbage resultater.

Vi giver et overblik over funktionen af hvordan dit skrider frem computer Learning model fra en udvikling eksperimentere til en operationalized webtjeneste indstillingen i denne artikel.

>[AZURE.NOTE] Der findes andre måder til at udvikle og implementere machine learning modeller, men i denne artikel fokuserer på, hvordan du bruger maskine læ Studio. Finde en beskrivelse af hvordan du opretter en klassisk skønnet webtjeneste med R, kan du se blogindlægget [Build og installere skønnet Web Apps ved hjælp af RStudio og Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Mens Azure Machine Learning Studio er udviklet til at hjælpe dig med at udvikle og installere en *skønnet analyse model*, er det muligt at bruge Studio til at udvikle et forsøg, der ikke indeholder en skønnet analyse model. Et forsøg kan for eksempel lige Indtast data, manipulere den og derefter sende resultaterne. Du kan installere dette ikke-skønnet forsøg som en webtjeneste, men det er en enklere proces, fordi forsøget ikke kursus eller en computer learning model for vundne ligesom en skønnet analyse forsøg. Selvom det ikke er den typiske bruge Studio på denne måde, får vi medtage dem i diskussionen, så vi kan give en komplet beskrivelse af, hvordan Studio fungerer.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Udvikle og implementere en skønnet webtjeneste

Her er de faser, der følger en typisk løsning, som du udvikle og installere den ved hjælp af Machine Learning Studio:

![Installation flow](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figur 1 - faser af en typisk skønnet analyse-model*

### <a name="the-training-experiment"></a>Forsøget kursus

***Kursus eksperimentere*** er den indledende fase af udvikling af din webtjeneste i Machine Learning Studio. Formålet med kursus forsøget er at give dig et sted at udvikle, test, forbedrer og til sidst undervise en machine learning-modellen. Du kan endda træne flere modeller samtidigt, som du kan finde den bedste løsning, men når du er færdig med at eksperimentere du vælger en enkelt modtager oplæring model og fjerne resten fra forsøget. Et eksempel på udvikling af en skønnet analyse eksperimentere, skal du se [udarbejde en skønnet analytics-løsning til kreditkontrol risikovurdering i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Skønnet forsøget

Når du har en erfaren model i din kursus forsøg, klik på **Angiv webtjeneste** , og vælg **Skønnet webtjeneste** i Machine Learning Studio til at starte processen med at konvertere din kursus forsøg til en ***skønnet forsøg***. Formålet med skønnet forsøget er at bruge din erfaren model til at give nye data med formålet med tiden blive opgivet som en Azure-webtjeneste.

Denne konvertering er gjort for dig gennem følgende trin:

-   Konvertere sæt af moduler, der bruges til uddannelse i et enkelt modul og gemmer den som en erfaren model

-   Fjerne overflødige moduler, ikke er relateret til pointmodel for kundeemner

-   Tilføje input- og outputområder porte, der anvender den endelige webtjeneste

Der kan være flere ændringer, du vil gøre til at forberede dit skønnet forsøg til at installere som en webtjeneste. Hvis du vil webtjeneste til at få vist kun et undersæt af resultater, kan du tilføje et spamfiltrering modul før outputport.

I denne konverteringsprocessen slettes ikke kursus forsøget. Når processen er fuldført, du har to faner i Studio: et til kursus forsøg og en for skønnet forsøget. Denne måde, du kan foretage ændringer til uddannelse forsøget, før du installerer din webtjeneste og genopbygge skønnet forsøget. Eller du kan gemme en kopi af kursus forsøg at starte en ny linje forsøg.

>[AZURE.NOTE] Når du klikker på **Skønnet webtjeneste** du starter en automatisk proces for at konvertere din kursus forsøg til en skønnet forsøg, og det fungerer godt i de fleste tilfælde. Hvis din kursus forsøg er kompleks (for eksempel, du har flere stier til kursus, som du sammenføje), kan du foretrækker at gøre denne konvertering manuelt. Se [konvertere et Machine Learning-kurser forsøg til en skønnet forsøg](machine-learning-convert-training-experiment-to-scoring-experiment.md)kan finde flere oplysninger.

### <a name="the-web-service"></a>Webtjenesten

Når du er tilfreds, at din skønnet forsøg er klar, kan du installere din tjeneste som enten en klassisk webtjeneste eller en ny webtjeneste baseret på Azure ressourcestyring. Klik på **Installere webtjeneste** for at giver muligheder for din model ved at installere det som en *Klassisk Machine Learning-webtjeneste*, og vælg **Installere webtjeneste [klassisk]**. Du skal installere som *Ny Machine Learning-webtjeneste*, skal du klikke på **Installere webtjeneste** , og vælg **Installere webtjeneste [ny]**. Brugere kan nu sende data til din model ved hjælp af webtjenesten REST-API og modtage tilbage resultaterne. Se, [hvordan du bruge en Azure Machine Learning webtjeneste, der er blevet installeret fra en computer, læ forsøg](machine-learning-consume-web-services.md)kan finde flere oplysninger.

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Ikke-typisk kassen: oprette en ikke-skønnet webtjeneste

Hvis ikke uddannelse af din forsøg en skønnet analyse model, og du behøver ikke at oprette både en kursus forsøg og en vurdering forsøg – der er kun én forsøg, og du kan installere den som en webtjeneste. Machine Learning Studio registrerer, om din forsøg indeholder en skønnet model ved at analysere de moduler, du har brugt.

Når du har nævnt på din forsøg og er tilfreds med den:

1.  Klik på **Angiv webtjeneste** , og vælg **Omskoling webtjeneste** - input- og noder føjes der automatisk

2.  Klik på **Kør**

3. Klik på **Installere webtjeneste** , og vælg **Installere webtjeneste [klassisk]** eller **Installere webtjeneste [ny]** afhængigt af det, du vil installere-miljø.

Din webtjeneste er nu installeret, og du kan få adgang til og administrere ligesom en skønnet webtjeneste.

## <a name="updating-your-web-service"></a>Opdatere din webtjeneste

Nu hvor du har installeret dit forsøg som en webtjeneste, hvad nu, hvis du vil opdatere den?

Det afhænger af, hvad du vil opdatere:

**Du vil ændre input eller output, eller du vil ændre, hvordan webtjenesten behandler data**

Hvis du ikke ændrer modellen, men blot ændrer hvordan webtjenesten håndterer data, kan du redigere skønnet forsøget og derefter klikke på **Installere webtjeneste** og vælge **Installere webtjeneste [klassisk]** eller **Installere webtjeneste [ny]** igen. Webtjenesten er stoppet, opdaterede skønnet forsøget installeres og webtjenesten genstartes.

Her er et eksempel: Antag, at din skønnet forsøg returnerer hele rækken af indtastede data med det forventede resultat. Du kan beslutte, at du vil webtjenesten til at lige returnerer resultat. Så kan du tilføje et **Projekt kolonner** modul i skønnet forsøget, lige før outputporten udelade kolonner end resultatet. Når du klikker på **Installere webtjeneste** og vælge **Installere webtjeneste [klassisk]** eller **Installere webtjeneste [ny]** igen, opdateres webtjenesten.

**Du vil henblik på omstilling modellen med nye data**

Hvis du vil holde computeren læ modellen, men du vil gerne henblik på omstilling det med nye data, har du to muligheder:

1.  **Henblik på omstilling modellen, mens webtjenesten kører** - Hvis du vil henblik på omstilling din model under den skønnet webtjenesten kører, kan du gøre dette ved at foretage nogle ændringer i kursus forsøg at gøre den til en ***omskoling forsøg***, og du kan installere den som en ** *omskoling* webtjeneste**. Flere oplysninger om, hvordan du gør dette, under [henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md).

2.  **Gå tilbage til den oprindelige kursus forsøg og brug forskellige kursus data til at udvikle din model** - din skønnet forsøg er knyttet til webtjenesten, men kursus forsøg er direkte knyttet ikke på denne måde. Hvis du ændrer oprindelige kursus forsøget, og klik på **Angiv webtjeneste**, oprettes der en *Ny* skønnet eksperimentere, når installeret, opretter en *Ny* webtjeneste. Det opdaterer ikke bare den oprindelige webtjeneste.

    Hvis du vil redigere kursus forsøget, åbne den og klikke på **Gem som** for at oprette en kopi. Dette vil lade være oprindelige kursus forsøget, skønnet forsøg og -webtjeneste. Nu kan du oprette en ny webtjeneste med dine ændringer. Når du har installeret den nye webtjeneste, du kan derefter afgøre, om du vil stoppe den forrige webtjeneste eller beholde den kører sammen med den nye liste.

**Du vil uddannelse af en anden model**

Hvis du vil foretage ændringer i din oprindelige skønnet forsøg, såsom at vælge en anden computer learning algoritme forsøg på en anden kursus metode osv og derefter skal du følge den anden procedure, der er beskrevet ovenfor til omskoling din model: Åbn kursus forsøget, klik på **Gem som** for at oprette en kopi, og start derefter ned nye stien til udvikling af din model , oprette skønnet forsøget og implementere webtjenesten. Dette vil oprette et nyt websted service ikke er relateret til den oprindelige - du kan bestemme, hvilke en eller begge dele til at køre.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om processen med at udvikle og forsøg i følgende artikler:

-   konvertering af forsøget - [konvertere en Machine Learning-kurser forsøg til en skønnet forsøg](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   implementering af webtjenesten - [Implementer en Azure Machine Learning-webtjeneste](machine-learning-publish-a-machine-learning-web-service.md)

-   omskoling modellen - [henblik på omstilling Machine Learning modeller fra et program](machine-learning-retrain-models-programmatically.md)

Eksempler på hele processen, i:

-   [Machine learning Selvstudium: oprette din første forsøg i Azure maskine læ Studio](machine-learning-create-experiment.md)

-   [Gennemgang: Udvikle en skønnet analytics-løsning for kredit risikovurdering i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)