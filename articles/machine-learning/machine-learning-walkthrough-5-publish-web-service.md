<properties
    pageTitle="Trin 5: Installer Machine Learning webtjenesten | Microsoft Azure"
    description="Trin 5 af udvikle en forudseende løsning gennemgang: installere en forudseende eksperiment i Machine Learning Studio som en webtjeneste."
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Gennemgang trin 5: Installation af Azure Machine Learning-webtjeneste

Dette er femte trin gennemgang skal [udarbejde en forudseende analytics løsning i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Oprette et arbejdsområde til Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Overfør eksisterende data](machine-learning-walkthrough-2-upload-data.md)
3.  [Opret et nyt forsøg](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Uddanne og evaluere modellerne](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Installation af webtjenesten**
6.  [Få adgang til webtjenesten](machine-learning-walkthrough-6-access-web-service.md)

----------

Hvis du vil give andre mulighed for at bruge den forudseende model, vi har udviklet i denne gennemgang, skal anvende vi den som en webtjeneste på Azure.

Indtil videre har vi eksperimenteret med uddannelse af vores model. Men den installerede service ikke længere skal gøre uddannelse – Den genererer forudsigelser af vundne brugerens input, der er baseret på vores model. Så vi gøre nogle forberedelser til at konvertere dette forsøg fra en ***uddannelse*** forsøg til en ***forudseende*** eksperimentere. 

Dette er en totrinsproces:  

1. Konverter den *uddannelse eksperimenter* har vi oprettet en *forudseende forsøg*
2. Implementere forudseende forsøget som en webtjeneste.

Men først skal vi beskære dette eksperiment lidt ned. Vi har to forskellige modeller i forsøget, men vi vil kun én model når vi implementere dette som en webtjeneste.  

Antag, at vi har besluttet, at modellen forstærket træ var bedre model skal bruges. Så den første ting at gøre er at fjerne [To klasse understøttelse af Vector maskine] [ two-class-support-vector-machine] modul og de moduler, der blev brugt til undervisning i den. Du vil lave en kopi af forsøget først ved at klikke på **Gem som** i bunden af forsøget lærredet.

Vi har brug at slette følgende moduler:  

- [Understøttelse af to klasse vektor maskine][two-class-support-vector-machine]
- [Træne Model] [ train-model] og [Score Model] [ score-model] moduler, der var tilsluttet til den
- [Normalisere Data] [ normalize-data] (begge af dem)
- [Evaluere Model][evaluate-model]

Vælg modulet og tryk på DELETE, eller Højreklik på modulet, og vælg **Slet**.

Nu vi er klar til at installere denne model ved hjælp af [To klasse forstærket beslutningstræ][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertere forsøget uddannelse til en forudseende forsøg

Konvertering til en forudseende eksperiment i tre trin:

1. Gem den model, vi har uddannet og derefter erstatte vores undervisningsmoduler
2. Trim forsøget for at fjerne moduler, der kun skal bruges til uddannelse
3. Definere, hvor webtjenesten accepterer input, og hvor den genererer output

Heldigvis alle tre trin, der kan gøres ved at klikke på **Angiv Web service** i bunden af forsøget lærredet (Vælg indstillingen **forudseende Web service** ).

Når du klikker på **Opret Web service**, sker der flere ting:

- Uddannet modellen gemmes som et enkelt modul **Uddannet Model** i paletten modul til venstre for lærredet eksperiment (du kan finde den under **Uddannet modeller**).
- Moduler, der blev brugt til uddannelse er fjernet. Mere specifikt:
  - [To klasse forstærket beslutningstræ][two-class-boosted-decision-tree]
  - [Tog Model][train-model]
  - [Opdele Data][split]
  - anden [Køre scriptet R] [ execute-r-script] modul, der blev brugt til testdata
- Gemte uddannet modellen føjes til forsøget.
- **Web service input** og **output til Web service** moduler er tilføjet.

> [AZURE.NOTE] Forsøget er blevet gemt i to dele under faner, der er tilføjet i toppen af lærredet eksperiment: oprindelige uddannelse forsøget er under fanen **uddannelse eksperimentere**, og nyoprettede forudseende forsøget under **Predictive eksperimentere**.

Vi har brug at tage et ekstra trin med denne bestemt eksperiment.
Vi har tilføjet to [Udføre R Script] [ execute-r-script] moduler til at give en vægtning funktion til data til uddannelse og prøvning. Vi behøver ikke at gøre det i den endelige model.

Machine Learning Studio fjernet én [Køre scriptet R] [ execute-r-script] modul, når det har fjernet den [opdelte] [ split] modul. Nu kan vi nu fjerne den anden og forbinde [Metadata Editor] [ metadata-editor] direkte til [Score Model][score-model].    

Vores eksperiment skulle nu se sådan ud:  

![Vundne uddannet model][4]  

> [AZURE.NOTE] Du tænker måske, hvorfor vi venstre UCI tysk kreditkortdata datasættet i forudseende eksperiment. Tjenesten skal bruge brugerens data, ikke det oprindelige datasæt, så hvorfor lade det oprindelige datasæt i modellen?
>
>Det er rigtigt, at tjenesten ikke har brug for de oprindelige kreditkortdata. Men den behøver skemaet for disse data, som indeholder oplysninger om, hvor mange kolonner der er, og hvilke kolonner der er numeriske. Dette skemaoplysninger er nødvendige til at fortolke brugerens data. Vi lader disse komponenter, der er forbundet, så det resultatmønster modul har dataset skemaet, når tjenesten kører. Data, der ikke er brugt, blot skemaet.  

Kør forsøget en sidste gang (Klik på **Kør**). Hvis du vil kontrollere, at modellen stadig fungerer, skal du klikke på outputtet af [Score Model] [ score-model] modul, og vælg **Visningsresultater**. Du kan se, at de oprindelige data vises, samt værdien kredit risiko ("scoret etiketter") og sandsynlighedsværdi resultatmønster ("scoret sandsynligheder".) 

## <a name="deploy-the-web-service"></a>Installation af webtjenesten

Du kan installere forsøget som en klassisk webtjeneste eller en ny webtjeneste baseret på Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Installere som en klassisk webtjeneste ###

Du skal installere en klassisk webtjeneste, der er afledt af vores eksperiment, klik **Installere Web Service** nedenfor lærredet, og vælg **Installere Web Service [Classic]**. Machine Learning Studio installerer forsøget som en webtjeneste og går til dashboard til denne webtjeneste. Her kan du vende tilbage til forsøget (**få vist** eller **få vist seneste**) og køre en simpel test af Web-tjenesten (se **Test webtjenesten** nedenfor). Der er også oplysninger til at oprette programmer, der kan få adgang til Web-tjenesten (mere om dette i næste trin i denne gennemgang).

![Web servicedashboard][6]

Du kan konfigurere tjenesten ved at klikke på fanen **konfiguration** . Her kan du redigere navnet på tjenesten, (den har meddelt navnet eksperiment som standard) og give dem en beskrivelse. Du kan også give flere fulde etiketter for input / output-kolonner.  

![Konfigurere Web-tjenesten][5]  

### <a name="deploy-as-a-new-web-service"></a>Installere som en ny webtjeneste.

Du skal installere en ny webtjeneste, der er afledt af vores eksperiment, og klik på **Installere Web Service** nedenfor lærredet og **Implementere webtjeneste [ny]**. Machine Learning Studio overfører til siden Azure Machine Learning Web services installerer eksperiment.

Angiv et navn til webtjenesten, og vælg en prissætning plan. Hvis du har en eksisterende prissætning plan, kan du vælge den, skal ellers du oprette en ny pris plan for tjenesten. 

1.  Vælg en eksisterende plan, eller Vælg indstillingen **Vælg ny plan** på rullelisten **Pris Plan** .
2.  Skriv et navn, der identificerer planen på din regning i **Modelnavn**.
3.  Vælg en af de **Månedlige planlægger metodetrin**. Plan metodetrin standard planer for dit standardområde og Web-service er installeret på dette område.

Klik på **Installer** og **hurtigstart** -siden til din Web-tjenesten åbnes.

Du kan konfigurere tjenesten ved at klikke på menupunktet **konfiguration** . Her kan du redigere navnet på tjenesten, (den har meddelt navnet eksperiment som standard) og give dem en beskrivelse. 

For at teste Vælg Web service, skal du klikke på menupunktet **Test** (se **Test webtjenesten** nedenfor). Oplysninger om oprettelse af programmer, der kan få adgang til webtjenesten, skal du klikke på menupunktet **forbrug** (mere om dette i næste trin i denne gennemgang).

> [AZURE.TIP] Når du har installeret det, kan du opdatere Web-tjenesten. For eksempel hvis du vil ændre din model, Rediger uddannelse forsøget, tweak parametrene model og klik på **Installere webtjeneste**. Vælg derefter **Installere Web Service [Classic]** eller **Installere [ny]-webtjenesten**. Når du installerer forsøget igen, erstatter den webtjeneste, der bruger nu opdateret modellen.  

## <a name="test-the-web-service"></a>Test af webtjenesten

### <a name="test-a-classic-web-service"></a>Teste en klassisk webtjeneste

Du kan teste tjenesten i Machine Learning Studio eller Azure Machine Learning Web Services-portalen. Test på Azure Machine Learning Web Services-portalen har fordelen, der gør det muligt at aktivere 

**Test i Machine Learning Studio**

Klik på knappen **Test** under **Standard slutpunkt**på **DASHBOARD** -siden. En dialogboks vil dukker op og beder dig om inddata til tjenesten. Disse er de samme kolonner, der blev vist i det oprindelige datasæt for tyske kredit risiko.  

Angiv et sæt data, og klik derefter på **OK**. 

**Test i Azure Machine Learning Web Services-portalen**

Klik på linket **Test** udskrift under **Standard slutpunkt**på **DASHBOARD** -siden. Testsiden i Azure Machine Learning Web Services portal til Web-tjenesteslutpunkt åbnes og beder om inddata til tjenesten. Disse er de samme kolonner, der blev vist i det oprindelige datasæt for tyske kredit risiko.

Klik på **Test anmodning-svar**. 

I webtjenesten, indtaster data via modulet **Web service input** via [Metadata Editor] [ metadata-editor] modul, og [Score Model] [ score-model] modul, hvor det er scoret. Resultaterne er derefter output fra webtjenesten via **Web service output**.

**Teste en ny webtjeneste**

Klik på **Test** i toppen af siden på portalen Azure Machine Learning Web services. **Testsiden** åbnes, og du kan angive data for tjenesten. Felterne input vises svarer til de kolonner, der vises i det oprindelige datasæt for tyske kredit risiko. 

Angiv et sæt data, og klik derefter på **Test anmodning-svar**.

Resultaterne af testen vises på højre side af siden i outputkolonnen. 

Når du tester på Azure Machine Learning Web Services-portalen, kan du aktivere eksempeldata, som du kan bruge til at teste tjenesten anmodning-svar. Hvis du har oprettet Web-tjenesten i Machine Learning Studio er eksempeldataene hentet fra dataene din bruges til at træne din model.

> [AZURE.TIP] Den måde, vi har konfigureret, forudseende forsøget hele resultater fra [Score Model] [ score-model] modul returneres. Dette omfatter alle inputdata plus værdien kredit risikoen og sandsynligheden for den resultatmønster. Hvis du ønsker at returnere noget andet - for eksempel kun kredit risikerer værdi - og derefter kan du indsætte et [Projekt kolonner] [ project-columns] modul mellem [Score Model] [ score-model] og **output til Web service** til at fjerne kolonner, du ikke vil webtjenesten til at vende tilbage. 

## <a name="manage-the-web-service"></a>Administration af webtjenesten

**Administrere en klassisk webtjeneste**

Når du har implementeret din klassiske webtjenesten, kan du administrere den fra [Azure klassiske portal](https://manage.windowsazure.com).

1. Log på [Azure klassiske portal](https://manage.windowsazure.com).
2. Klik på **MACHINE LEARNING**i panelet Azure Microsoft-tjenester.
3. Klik på dit arbejdsområde.
4. Klik på fanen **Web serviceS** .
5. Klik på den webtjeneste, der er oprettet.
6. Klik på "standard"-slutpunkt.

Herfra kan du udføre handlinger som overvåger hvordan klarer webtjenesten og foretage justeringer af ydeevne ved at ændre, hvor mange samtidige ringer til tjenesten kan håndtere.
Du kan også udgive Web-tjenesten på Azure Marketplace.

Yderligere oplysninger finder du under:

- [Oprette slutpunkter](machine-learning-create-endpoint.md)
- [Skalering af webtjenesten](machine-learning-scaling-webservice.md)
- [Udgive Azure Machine Learning webtjeneste på Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Administrere en webtjeneste i Azure Machine Learning Web Services portal**

Når du har implementeret Web service, klassisk eller ny, kan du administrere den fra [Azure Machine Learning Web services portal](https://servics.azureml.net).

Overvåge ydeevnen for webtjenesten:

1. Log på [Azure Machine Learning Web services portal](https://servics.azureml.net).
2. Klik på **Web-tjenester**.
3. Klik på Web-service.
4. Klik på **Dashboard**.

----------

**Næste: [adgang til webtjenesten](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/