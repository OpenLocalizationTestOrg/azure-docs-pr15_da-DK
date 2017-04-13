<properties
    pageTitle="Konvertere en Machine Learning-kurser forsøg til en skønnet forsøg | Microsoft Azure"
    description="Sådan konverteres et Machine Learning kursus forsøg, der bruges til uddannelse din skønnet analytics-model, til en skønnet forsøg, som kan anvendes som en webtjeneste."
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
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Konvertere en Machine Learning-kurser forsøg til en skønnet forsøg

Azure Machine Learning gør det muligt at oprette, teste og implementere skønnet analytics løsninger.

Når du har oprettet og nævnt på et *kursus eksperimentere* til at undervise skønnet analytics modellen, og du er klar til at bruge det til at give nye data, skal du til at forberede og strømline din forsøg resultatværdier. Du kan installere dette *skønnet forsøg* som en Azure-webtjeneste, så brugerne kan sende data til din model og modtage din model prognoser.

Ved at konvertere til en skønnet forsøg, får du erfaren modellen klar til at være installeret som en webtjeneste. Brugere af webtjenesten sender indtastede data til din model, og din model sende tilbage forudsigelse resultaterne. Så når du konverterer til en skønnet forsøg skal du huske på, hvordan du forventer din model, der skal bruges af andre.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Processen med at konvertere en kursus forsøg til en skønnet forsøg omfatter tre trin:

1.  Gem machine learning modellen, som du har uddannelse, og erstat derefter den maskine, læ algoritme og [Tog Model] [ train-model] moduler med din gemte erfaren model.
2.  Trimme forsøget til kun disse moduler, der skal bruges til pointmodel for kundeemner. Et kursus forsøg omfatter et antal moduler, der er nødvendige for kursus, men som ikke er nødvendige, når modellen er erfaren og klar til at bruge resultatværdier.
3.  Definere, hvor i din forsøg du accepterer data fra web servicebrugeren, og hvilke data der skal returneres.

## <a name="set-up-web-service-button"></a>Angive webtjeneste knappen

Når du har kørt din forsøg (knappen**Kør** nederst på lærredet forsøg), udfører knappen **Angive webtjeneste** (Vælg indstillingen **Skønnet webtjeneste** ) for dig de tre trin til at konvertere din kursus forsøg til en skønnet forsøg:

1.  Gemmer den erfaren modellen, som et modul i afsnittet **Erfaren modeller** i paletten modul (til venstre for forsøg lærredet), erstatter den maskine, læ algoritme og [Tog Model] [ train-model] moduler med gemte erfaren modellen.
2.  Det fjerner moduler, der ikke er klart er nødvendigt. I vores eksempel dette omfatter de [Opdelte Data][split], 2. [Karakteren Model][score-model], og [Evaluere Model] [ evaluate-model] moduler.
3.  Det opretter Web service input og output moduler og lægger dem i standardplaceringer i din forsøg.

Følgende forsøget træner for eksempel en to-klasse øget beslutning træet model, ved hjælp af tælling eksempeldata:

![Kursus forsøg][figure1]

Moduler i dette forsøg udføre grundlæggende fire forskellige funktioner:

![Modul funktioner][figure2]

Når du konverterer dette kursus forsøg til en skønnet forsøg, nogle af disse moduler ikke længere skal bruges, eller de har forskellige ting:

- **Data** - dataene i dette eksempel dataset og bruges ikke under vundne - brugeren af webtjenesten skal levere dataene skal bedømmes. Dog der metadata fra dette dataset som datatyper, bruges af erfaren modellen. Så skal du holde datasættet i skønnet forsøget, så den kan give disse metadata.

- **Forberedelse** - afhængigt af de data, der sendes til vundne, disse moduler kan eller ikke kan være nødvendigt at behandle indgående data.

    I dette eksempel muligvis eksempel datasættet manglende værdier, og den indeholder kolonner, der ikke skal bruges til at undervise modellen. Så en [Ren manglende Data] [ clean-missing-data] modul fulgte handlen med manglende værdier, og [Vælg kolonner i Dataset] [ select-columns] modul blev leveret til at udelukke disse ekstra kolonner fra dataflow. Hvis du ved, at de data, der sendes til vundne via webtjenesten ikke vil have manglende værdier og derefter kan du fjerne [Ren manglende Data] [ clean-missing-data] modul. Men siden [Vælg kolonner i Dataset] [ select-columns] modul hjælper med at definere sættet af funktioner, der gives point, modulet skal forblive.

- **Tog** - når modellen er blevet accepteret, du gemme det som et enkelt erfaren model modul. Du kan derefter erstatte disse individuelle moduler med gemte erfaren modellen.

- **Resultat** – bruges i dette eksempel modulet opdelt til at inddele datastrømmen i et sæt af testdata og uddannelsesdata. I skønnet forsøget dette er nødvendigt ikke og kan fjernes. På samme måde, 2. [Karakteren Model] [ score-model] modul og [Evaluere Model] [ evaluate-model] modul, der bruges til at sammenligne resultater fra testdata, så disse moduler ikke er også er nødvendigt i skønnet forsøget. Resterende [Karakteren Model] [ score-model] modul, men skal bruges til at returnere et resultat resultat via webtjenesten.

Her er, hvordan vores eksempel ser ud efter at klikke på **Angiv webtjeneste**:

![Konverterede skønnet forsøg][figure3]

Det kan være tilstrækkelige til at forberede dit forsøg skal installeres som en webtjeneste. Dog kan du udføre nogle ekstra arbejde, der er specifikke for din forsøg.

### <a name="adjust-input-and-output-modules"></a>Justere input- og outputområder moduler

Du har brugt et sæt kursus data og derefter har nogle behandling for at få vist data i en formular, den maskine learning algoritme skal bruges i din kursus forsøg. Hvis de data, du forventer at modtage via webtjenesten ikke behøver denne behandling, kan du flytte **Web input service-modulet** til en anden node i din forsøg.

For eksempel som standard indsætter **Angive webtjeneste** modulet **Web service input** øverst på din dataflow, som i den figur, der er ovenfor. Men hvis inputdataene ikke behøver denne behandling, derefter kan du manuelt placere **Web service input** tidligere databehandling moduler:

![Flytte web service input][figure4]

Inputdataene via webtjenesten nu overfører direkte i modulet karakteren Model uden en hvilken som helst forbehandling.

På samme måde som standard indsætter **Angive webtjeneste** modulet Web service output nederst i din dataflow. I dette eksempel webtjenesten vender tilbage til brugeren output fra [Karakteren Model] [ score-model] modul, der indeholder den komplette inputdata vektor plus de vundne resultater.

Hvis du foretrækker at returnere noget forskelligt – for eksempel, kun de vurdering resultater og ikke hele vektoren for indtastede data- og du kan dog indsætte en [Vælg kolonner i Dataset] [ select-columns] modul til at udelukke alle kolonner undtagen vurdering resultaterne. Du flytter derefter modulet **Web service output** til output fra [Vælg kolonner i Dataset] [ select-columns] modul:

![Flytte web service output][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Tilføje eller fjerne ekstra databehandling moduler

Hvis der er flere moduler i din forsøg, som du ved, at der ikke skal bruges under pointmodel for kundeemner, kan disse fjernes. For eksempel fordi vi har flyttet modulet **Web service input** til et punkt efter databehandling moduler, kan vi fjerne [Ren manglende Data] [ clean-missing-data] modul fra skønnet forsøget.

Vores skønnet forsøg ser nu sådan ud:

![Fjerne ekstra modul][figure6]

### <a name="add-optional-web-service-parameters"></a>Tilføje valgfrie Web Serviceparametre

I nogle tilfælde kan du vil tillade, at brugeren af din webtjeneste til at ændre funktionsmåden for moduler, når der opnås adgang til tjenesten. *Web Serviceparametre* giver dig mulighed at gøre dette.

Et almindeligt eksempel er ved at [Importere Data] [ import-data] modul så brugeren af udløst webtjenesten kan angive en anden datakilde, når webtjenesten åbnes. Eller konfiguration af [Eksporter Data] [ export-data] modul så du kan angive en anden destination.

Du kan definere parametre for Web og knytte dem til en eller flere modul parametre, og du kan angive, om de er nødvendig eller valgfri. Brugeren af webtjenesten kan derefter angive værdier for disse parametre, når der opnås adgang til tjenesten og modul handlinger vil blive ændret i overensstemmelse hermed.

Få vist [Ved hjælp af Azure Machine Learning Web Serviceparametre ]kan finde flere oplysninger om Web Serviceparametre [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Installere skønnet forsøget som en webtjeneste

Nu hvor skønnet forsøget er tilstrækkeligt forberedt, kan du installere det som en Azure-webtjeneste. Ved hjælp af webtjenesten, brugerne kan sende data til din model og modellen, returnerer dens prognoser.

Yderligere oplysninger om fuldført installationsprocessen, se [Implementer en Azure Machine Learning-webtjeneste][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
