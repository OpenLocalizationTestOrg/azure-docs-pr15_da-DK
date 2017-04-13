<properties 
    pageTitle="Hvad er Team Data videnskabelige proces?  | Microsoft Azure" 
    description="Team Data videnskabelige processen er en systematisk metode til oprettelse af intelligente programmer, der udnytter avanceret analyse." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="bradsev" /> 


# <a name="what-is-the-team-data-science-process-tdsp"></a>Hvad er Team Data videnskabelige proces (TDSP)?

[Team Data videnskabelige proces (TDSP)](data-science-process-overview.md) indeholder en systematisk fremgangsmåde til at lave intelligente programmer, der gør det muligt for teams for videnskabelige eksperter data til at samarbejde effektivt fulde levetid af aktiviteter, der er behov for at slå disse programmer til produkter. TDSP beskriver en række trin, der giver **vejledning** om, hvordan du definerer problem, skal du konfigurere værktøjerne og det er nødvendigt, analysere relevante data, oprette og evaluere skønnet modeller og derefter installere disse modeller i enterprise-programmer-miljø. 

Her er trinnene i **Team Data videnskabelige processen**:  

![KNOP-arbejdsproces](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

Processen er **gentaget**: forståelse af nye og eksisterende eller begrænsninger i modellen udvikler sig og kræver reworking trin, der tidligere har færdiggjort, i hvilken rækkefølge. Eksisterende organisatoriske udvikling og projektplanlægning processer er **tilpasset nemt** at arbejde med den TDSP brugerdefineret række trin. 

Trinnene i processen, der afbildes i et diagram og sammenkædet i [TDSP læringssti](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) og beskrevet nedenfor.  

## <a name="preparation-steps"></a>Forberedelse 

## <a name="p1-plan-the-analytics-project"></a>P1. Planlægge analytics-projekt 

Starte en analytics-projekt ved at definere dine forretningsmæssige mål og problemer. De er angivet med hensyn til **virksomhedens behov**. Et centralt mål for dette trin er at identificere de vigtigste business variabler (salg prognose eller sandsynligheden en rækkefølge er falsk, for eksempel), som analysen skal forudsige for at opfylder kravene. Yderligere planlægning er derefter normalt vigtigt at udvikle en forståelse af de **datakilder** , der er behov for at løse formålet med projektet fra en analytisk perspektiv. Det er ikke usædvanlige, for eksempel at finde, eksisterende systemer skal indsamling og logge flere typer data, der løser problemet og opnå project mål. Vejledning i [planlægge dit miljø til Team Data videnskabelige processen](machine-learning-data-science-plan-your-environment.md) og [scenarier, hvor avanceret analyse i Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md).  

## <a name="p2-setup-analytics-environment"></a>P2. Konfiguration af analytics-miljø 

En analytics-miljø til Team Data videnskabelige processen omfatter flere komponenter: 

- **data arbejdsområder** , hvor dataene er midlertidigt gemt til analyse og modellering, 
- en **behandling af infrastruktur** til forbehandling af, udforske og modeling dataene
- en **runtime infrastruktur** giver muligheder for analytical modeller og køre de intelligente klientprogrammer, der forbruger modellerne.  

Analytics-infrastrukturen, der skal være konfiguration er ofte en del af et miljø, der er adskilt fra funktionsdygtige core-systemer. Men det typisk bruger data fra flere systemer i virksomheden og fra kilder uden for virksomheden. Analytics-infrastruktur kan være rent skybaseret, eller en lokal installation eller en hybrid af to. Finde oplysninger i [konfigurere data videnskabelige miljøer til brug i gruppen Data videnskabelige processen](machine-learning-data-science-environment-setup.md).

## <a name="analytics-steps"></a>Analytics trin:  

## <a name="1-ingest-data-into-the-analytical-environment"></a>1. indtager Data i analytical miljøet 

Det første trin er at hente de relevante data fra forskellige kilder, enten fra eller fra uden for virksomheden, til en analytisk miljøer, hvor dataene kan behandles. **Formatér** data på kilde kan variere fra det format, som destinationen. Nogle datatransformation muligvis så også udføres af indtagelse værktøj. Indstillinger for at se [Indlæs data til lagerplads miljøer for analyser](machine-learning-data-science-ingest-data.md)

Mange intelligente programmer er påkrævet for at opdatere dataene jævnligt som en del af en igangværende learning ud over den indledende indtagelse af data. Dette kan gøres ved at konfigurere en **data pipeline** eller arbejdsproces. Dette udgør en del af den gentagende del af den proces, der omfatter genopbygge og igen evaluering af de analytical modeller, der bruges af intelligente programmet implementering af løsningen. Se for eksempel [flytte data fra en lokal SQL server til SQL Azure med Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md).


## <a name="2-explore-and-pre-process-data"></a>2. udforske og allerede behandle data 

Næste trin er at få en bedre forståelse af dataene ved at undersøge dens **Oversigt statistik** , relationer, og ved hjælp af teknikker sådanne **visualisering**. Dette er også hvor problemer af **datakvalitet** og integritet som manglende værdier, datatyperne og modstridende data relationer skal håndteres. Forbehandling transformationer bruges til at rydde op i rækkedata før yderligere analyser og modellering kan finde sted. Finde en beskrivelse, [opgaver for at forberede data til udvidet maskine læ](machine-learning-data-science-prepare-data.md).


## <a name="3-develop-features"></a>3. udvikle funktioner 

Data videnskabsfolk, i samarbejde med domæne eksperter skal identificere de funktioner, der registrere vigtige egenskaberne for datasættet og, der kan bedst bruges til at forudsige vigtige business variablerne identificeret under planlægningen. Disse nye funktioner kan udledes fra eksisterende data eller kræver muligvis yderligere data indsamles. Denne proces, der kendes som **funktion teknisk** og er et af de vigtigste trin i opbygning af en effektiv skønnet analysesystem. Dette trin kræver en kreativ kombination af domæne ekspertise og de viden, som fås fra trinnet data udforske. Vejledning i [funktionen teknisk Team Data videnskabelige processen](machine-learning-data-science-create-features.md).


## <a name="4-create-predictive-models"></a>4. oprette skønnet modeller 

Data forskere opbygge analytical modeller for at forudsige de vigtigste variabler, der er identificeret med de forretningsbehov, der er defineret i planlægning trin ved hjælp af data, som er gjort rent og featurized. Machine learning systemer understøtter flere **modeling algoritmer** , der gælder for en lang række sager. Se, [hvordan du vælger algoritmer til Team Azure Machine Learning](machine-learning-algorithm-choice.md)vejledning.

Data forskere skal vælge den mest relevante model for deres forudsigelse opgave, og det er ikke usædvanlige, resultater fra flere modeller skal kombineres for at opnå de bedste resultater. Skriv dataene til modellering er som regel inddelt tilfældigt i tre dele:

- datasættet kursus 
- datasættet validering 
- datasættet test 

Modellerne oprettes ved hjælp af **kursus datasæt**. Optimal kombinationen af modeller (med parametre indstillet) er valgt ved at køre modellerne og måling forudsigelse fejl i **Validering datasæt**. Til sidst på **teste datasæt** , der bruges til at evaluere ydeevnen for den valgte model på uafhængige data, der ikke blev brugt til at undervise eller validere modellen.  For procedurer, skal du se, [hvordan du evaluere model ydeevne i Azure Machine Learning](machine-learning-evaluate-model-performance.md).


## <a name="5-deploy-and-consume-models"></a>5. installere og forbruge modeller 

Når vi har et sæt af modeller, som udfører godt, kan de være **opgivet** andre programmer kan bruge. Afhængigt af forretningsbehov er prognoser foretaget i **realtid** eller på grundlag af **batchen** . Hvis du vil være opgivet, modellerne skal være vises med en **åbne API grænseflade** , der er nemt consumed fra forskellige programmer sådanne online-websted, regneark, dashboards eller line of business- og back end-applikationer. Se [Implementer en Azure Machine Learning webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="summary-and-next-steps"></a>Oversigt og næste trin

[Team Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) er formateret som en sekvens af nævnt trin, der **giver vejledning** om opgaverne, der skal bruges til at bruge avanceret analyse til at oprette en intelligent programmer. Hvert trin indeholder også oplysninger om, hvordan du bruger forskellige Microsoft-teknologier til at udføre de opgaver, der er beskrevet. 

Mens TDSP ikke foreskriver bestemte typer af **dokumentation** elementer, det er en god fremgangsmåde til at dokumentere resultaterne af undersøgelse af data, modellering og evaluering, og for at gemme den relevante kode, så analysen kan nævnt når det er påkrævet. Dette kan også genbrug af analytics arbejdet, når du arbejder på andre programmer, der involverer ensartede data og tekstfuldførelse opgaver.

Fuldstændig til en komplet gennemgange, der viser alle trin i processen for **bestemte scenarier** , der findes også. Se, f.eks.:

- [Team Data videnskabelige processen i praksis: Brug af SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [Team Data videnskabelige proces i praksis: Brug af HDInsight Hadoop klynger](machine-learning-data-science-process-hive-walkthrough.md).
- [Data videnskabelige ved hjælp af knallertmotor på Azure HD.mdnsight](machine-learning-data-science-spark-overview.md)
- [SVG Data videnskab i Azure Data sø: en gennemgang af til slut](machine-learning-data-science-process-data-lake-walkthrough.md)

 
