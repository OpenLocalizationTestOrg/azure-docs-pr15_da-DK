<properties
    pageTitle="Analyse af kunde Churn ved hjælp af Machine Learning | Microsoft Azure"
    description="Casestudie af udvikling af en integreret model til at analysere og vundne kunde transportspand"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analysere kunde Churn ved hjælp af Azure Machine Learning

##<a name="overview"></a>Oversigt
Dette emne indeholder en referenceimplementering af et projekt i kunde transportspand analyse, der er oprettet ved hjælp af Azure Machine Learning Studio. Den behandler tilknyttede generisk modeller til at løse problemet med industrielle kunde transportspand holistically. Vi også mål for nøjagtigheden af modeller, der er oprettet ved hjælp af Machine Learning, og vi Vurder kørselsvejledning til yderligere udvikling.  

### <a name="acknowledgements"></a>Godkendelser

Dette forsøg blev udviklet og testet ved Serge ville hovedstolen Data Scientist hos Microsoft og Roger Barga, tidligere Product Manager til Microsoft Azure Machine Learning. Azure dokumentationsteamet meget taknemmelige accepterer deres ekspertise og Tak dem til at dele denne hvidbog.

>[AZURE.NOTE] De data, der bruges til dette forsøg er ikke offentligt tilgængeligt. Se et eksempel på, hvordan du opretter en machine learning model til transportspand analyse: [Telco churn model skabelon](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) i [Cortana Intelligence galleri](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>Problemet med kunde transportspand
Virksomheder i consumer markedet og i alle enterprise sektorer nødt til at håndtere transportspand. Nogle gange transportspand er for lang tid og påvirker politik beslutninger. Den traditionelle løsning er at forudsige høj tilbøjeligheden churners og deres behov via en concierge tjeneste, marketingkampagner, eller ved at anvende særlige dispensationer en adresse. Disse fremgangsmåder kan variere fra branche til branche og endda fra en bestemt consumer klynge til en anden inden for en branche (for eksempel telekommunikation).

Almindelige faktor er, at virksomheder har behov at minimere indsats disse speciel customer opbevaring. Dermed, en fysisk metode ville være at resultat hver kunde med sandsynligheden for transportspand og tag fat i den øverste N dem. De bedste kunder kan være mest rentable dem; for eksempel er mere avancerede scenarier, en avance funktion ansat under markeringen af kandidater til særlige dispensation. Disse overvejelser er dog kun en del af den holistiske strategi for håndtering af transportspand. Virksomheder, der har også at tage konto risiko (og tilknyttede risiko tolerance) niveau og omkostninger af brugerinput og troværdige kunde segmentering.  

##<a name="industry-outlook-and-approaches"></a>Branche outlook og fremgangsmåder
Avancerede håndtering af transportspand er en logge af en fuldt udviklet branche. Klassisk eksemplet er telekommunikationsindustrien hvor abonnenter kendte ofte skifte fra én udbyder til en anden. Denne frivillige transportspand er en vigtigste problem. Desuden har udbydere akkumulerede betydeligt viden om *churn drivere*, som er faktorerne, der drev kunder i at skifte.

For eksempel er håndsæt eller enhed valg en velkendt driver af transportspand i virksomheden mobiltelefon. Som et resultat er en populære politik at yder prisen på et håndsæt for nye abonnenter og opladning en fuld pris til eksisterende kunder til en opgradering. Historisk set har denne politik ført til kunder, der er hopping fra én udbyder til en anden for at få en ny rabat, som også har bedt om at afgrænse deres strategier udbydere.

Høj flygtighed håndsæt tilbud er en faktor, der meget hurtigt ugyldiggør modeller af transportspand, der er baseret på aktuelle håndsæt modeller. Desuden er mobiltelefoner ikke kun telekommunikationsmæssige enheder de er også måde sætninger (overveje iPhone), og disse sociale predictors er uden for omfanget af almindelige telekommunikation datasæt.

Dermed for modellering er, at du ikke kan udtænke en lyd politik blot ved at fjerne kendte årsager til transportspand. Faktisk er en fortløbende modellering strategi, herunder klassisk modeller sæt tal på kategoriske variabler (såsom beslutningstræer), **obligatorisk**.

Ved hjælp af store datasæt på deres kunder, udfører organisationer stor data analytics (især transportspand registrering baseret på stor data) som en effektiv metode til problemet. Du kan finde flere oplysninger om stor data metoden til at problemet med transportspand i anbefalinger på ETL sektion.  

##<a name="methodology-to-model-customer-churn"></a>Metoder til at model kunde transportspand
En fælles problemløsning proces til at løse kunde transportspand er afbildet i tal 1-3:  

1.  En risikomodel kan du overveje, hvordan handlinger påvirker sandsynligheden og risiko.
2.  En brugerinput model kan du overveje, hvordan niveauet af brugerinput, kan det påvirke sandsynligheden for transportspand og mængden kunde levetiden (CLV).
3.  Denne analyse bruges til en kvalitativ analyse, der er videresendt til en proaktiv marketingkampagne, der er beregnet til kunde målgrupper for at levere optimal tilbuddet.  

![][1]

Denne fremad udseende metode er den bedste måde at behandle transportspand, men den leveres med kompleksitet: Vi har til at udvikle en flere modeller archetype og sporing afhængigheder mellem modellerne. Interaktionen mellem modeller kan være encapsulated, som vist i følgende diagram:  

![][2]

*Figur 4: Samlet flere modeller archetype*  

Interaktioner mellem modellerne er nøgle, hvis vi skal levere en holistisk tilgang til kunde opbevaring. Hver model forringer nødvendigvis med tiden; arkitekturen er derfor en implicit løkke (svarer til den archetype, der er angivet af SKARPE DM data mining standard, [***3***]).  

Den overordnede livscyklussen for risikoen-beslutning-marketing segmentering/opdelingstræ er stadig en generalized struktur, som er gældende for mange forretningsmæssige problemer. Transportspand analyse er blot en stærk repræsentant for denne gruppe af problemer, fordi den opfører alle egenskaber for et komplekst business-problem, der ikke tillader en forenklet skønnet løsning. De sociale aspekter af den moderne metode til at churn er ikke særligt fremhævet i fremgangsmåden, men de sociale aspekter er encapsulated i archetype modellering, som de skal være i en model.  

En interessant tilføjelse er stor data analyser. Dags telekommunikationsmæssige og detailsalg virksomheder indsamler omfattende data om deres kunder, og vi nemt kan forudse, bliver brug for flere modeller connectivity en fælles tendens givet nye tendenser som Internet ting og almindelig enheder, der giver mulighed for business til at anvende Smart-løsninger på flere lag.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementere modellering archetype i Machine Learning Studio
Givet det problem, der netop er beskrevet, hvad er den bedste måde at implementere en integreret modellering og vundne tilgang? I dette afsnit skal demonstrerer vi, hvordan vi opnås dette ved hjælp af Azure Machine Learning Studio.  

Flere modeller metode egner sig en most, når du designer en global archetype for transportspand. Selv den resultat (skønnet) del af metoden skal være flere modeller.  

I det følgende diagram viser prototypen, vi oprettede, som anvender fire vurdering algoritmer i Machine Learning Studio til at forudsige transportspand. Årsagen til at bruge en flere modeller fremgangsmåde er ikke kun til at oprette en ensemble klassificering til at forbedre nøjagtighed, men også at beskytte mod overliggende tilpasse og forbedre præskriptive funktion markering.  

![][3]

*Figur 5: Prototyper af en transportspand modeling tilgang*  

De følgende afsnit indeholder flere oplysninger om prototyper vundne model, vi har implementeret ved hjælp af Machine Learning Studio.  

###<a name="data-selection-and-preparation"></a>Valg af data og forberedelse
Dataene, der bruges til at oprette modellerne og karakteren kunder blev hentet fra en lodret CRM-løsning med de data, korrumperet for at beskytte kunde beskyttelse af personlige oplysninger. Data, der indeholder oplysninger om 8.000 abonnementer i USA, og den kombinerer tre kilder: klargøring af data (abonnement metadata), aktivitetsdata (brugen af systemet) og understøttelse af kundedata. Data, der ikke omfatter en hvilken som helst business relaterede oplysninger om kunderne for eksempel inkluderer det ikke loyalitet metadata eller kredit resultater.  

For enkel, er ETL og dataudrensningen processer uden for området fordi vi antager, at forberedelse af data er allerede blevet udført et andet sted.   

Valg af funktion for modellering er baseret på foreløbige betydning for vundne over sættet af predictors, medtages i den proces, der bruger modulet tilfældige skov. Ved anvendelsen i Machine Learning Studio skal beregnet vi gennemsnit, median og områder for repræsentant funktioner. For eksempel tilføjet vi samlinger kvalitativ data, som mindste og største værdier for brugeraktivitet.    

Vi hentes også tidsmæssig oplysninger om de seneste seks måneder. Vi analysere data i et år, og vi etableret, selvom der ikke er statistisk signifikant tendenser, indvirkning på transportspand er meget formindsket efter seks måneder.  

Det vigtigste er, at hele processen, herunder ETL, valg af funktion og -modellering blev implementeret i Machine Learning Studio, ved hjælp af datakilder i Microsoft Azure.   

Følgende diagrammer illustrerer de data, der blev brugt.  

![][4]

*Figur 6: Uddrag af datakilde (korrumperet)*  

![][5]


*Figur 7: Funktioner, der er hentet fra datakilde*
 
> Bemærk, at disse data er privat, og derfor modellen og data kan ikke deles.
> Men se dette eksempel eksperimentere i [Cortana Intelligence galleriet](http://gallery.cortanaintelligence.com/)for en lignende model ved hjælp af offentligt tilgængelige data,: [Telco kunde Churn](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Hvis du vil vide mere om, hvordan du kan implementere en transportspand analysis model, der bruger Cortana Intelligence pakke, anbefaler vi også [i denne video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) ved Senior Program Manager western Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algoritmer, der bruges i prototypen

De følgende fire machine learning algoritmer brugte vi til at oprette prototyper (ingen tilpasning):  

1.  Logistisk regression (LR)
2.  Øget beslutningstræ (BT)
3.  Gennemsnitlig perceptron (Adgangspunkt)
4.  Understøttelse af vektor maskine (SVM)  


I følgende diagram vises en del af designoverfladen forsøg, som angiver, hvilken rækkefølge, hvori modellerne, der blev oprettet:  

![][6]  


*Figur 8: Oprette modeller i Machine Learning Studio*  

###<a name="scoring-methods"></a>Vundne metoder
Vi karakter for de fire modeller ved hjælp af et mærket kursus datasæt.  

Vi også sendes til en tilsvarende model, der er oprettet ved hjælp af den stationære udgave af SAS Enterprise tillægsydelser minearbejdere 12 vurdering datasættet. Vi målt nøjagtigheden af SAS modellen og alle fire Machine Learning Studio-modeller.  

##<a name="results"></a>Resultater
I dette afsnit skal præsentere vi vores resultater om nøjagtigheden af de modeller, baseret på vurdering datasættet.  

###<a name="accuracy-and-precision-of-scoring"></a>Nøjagtigheden og præcision pointmodel for kundeemner
Generelt er implementeringen af i Azure Machine Learning bag SAS i nøjagtigheden af omkring 10-15% (område Under kurve eller AUC).  

De vigtigste metrikværdi i transportspand er dog misclassification rente: det vil sige, af de øverste N churners som estimerede af klassificeringen, hvilke af dem rent faktisk blev **ikke** transportspand og endnu modtaget særbehandling? I det følgende diagram sammenligner denne misclassification sats for alle modeller:  

![][7]


*Figur 9: Passau prototyper arealet under kurven*

###<a name="using-auc-to-compare-results"></a>Brug af AUC til at sammenligne resultater
Området Under kurve (AUC) er en metrikværdi, der repræsenterer et globale mål for *separability* mellem fordeling af pointtal for positive og negative populationer. Den ligner traditionelle modtager Operator egenskab (ROC) grafen, men en vigtig forskel er, at AUC metrisk ikke kræver dig om at vælge en grænseværdi. I stedet opsummerer den resultaterne på **alle** mulige indstillinger. I modsætning traditionelle ROC grafen viser den positive rente på den lodrette akse og FALSK positiv hastigheden på den vandrette akse, og i grænseværdi for klassifikation varierer.   

AUC bruges normalt som et mål for værd for forskellige algoritmer (eller forskellige systemer), fordi den tillader modeller skal sammenlignes ved hjælp af deres AUC værdier. Dette er en populære fremgangsmåde i virksomheder som meteorologi og biosciences. Derfor repræsenterer AUC et populære værktøj til vurdering af klassificering ydeevne.  

###<a name="comparing-misclassification-rates"></a>Sammenligne misclassification satser
Vi sammenlignes misclassification satser på det pågældende dataset ved hjælp af cirka 8.000 abonnementer CRM data.  

-   SAS misclassification rente var 10-15%.
-   Machine Learning Studio misclassification rente blev 15-20% for toppen 200-300 churners.  

I telekommunikationsindustrien er det vigtigt at udbedre kun de kunder, der har den største risiko til churn ved at give dem et concierge tjeneste eller andre særbehandling. I denne forbindelse opnås Machine Learning Studio implementeringen resultater on par with SAS modellen.  

Nøjagtigheden er af samme grund vigtigere end præcision, fordi vi er mest interesseret i korrekt klassificering potentielle churners.  

Følgende diagram fra Wikipedia viser relation i en spændende og nemt at forstå grafik:  

![][8]

*Figur 10: Fordeling nøjagtighed*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Nøjagtighed resultater for øget beslutning træet model  

Det følgende diagram viser rå resultaterne fra vundne ved hjælp af Machine Learning prototyper for øget beslutning træet modellen, som er den mest præcise mellem de fire modeller:  

![][9]

*Figur 11: Øget beslutning træet model egenskaber*

##<a name="performance-comparison"></a>Sammenligning af ydeevne
Vi sammenlignes hastigheden, hvor data er blevet karakter ved hjælp af Machine Learning Studio-modeller og en tilsvarende model, der er oprettet ved hjælp af den stationære udgave af SAS Enterprise tillægsydelser minearbejdere 12.1.  

Den følgende tabel opsummerer algoritmerne ydeevne:  

*Tabel 1. Generelle ydeevne (nøjagtigheden) af algoritmerne*

| LR|BT|ADGANGSPUNKT|SVM|
|---|---|---|---|
|Gennemsnitlig Model|Den bedste Model|Sig dårligere end forventet|Gennemsnitlig Model|

Modellerne Machine Learning Studio langt bedre SAS med 15-25% til hastigheden for udførelse af, men nøjagtigheden var stort set på nominel som vært.  

##<a name="discussion-and-recommendations"></a>Diskussion og anbefalinger
I telekommunikationsindustrien, der er opstået flere fremgangsmåder for at analysere transportspand, herunder:  

-   Afledt målepunkter for fire grundlæggende kategorier:
    -   **Enhed (for eksempel et abonnement)**. Klargøre grundlæggende oplysninger om abonnement og/eller kunde, som er emnet for transportspand.
    -   **Aktivitet**. Indhente alle mulige brugen oplysninger, der er relateret til enheden, for eksempel antal logon.
    -   **Kundesupport**. Indsamling af oplysninger fra kunde support logfiler til at angive, om abonnementet havde problemer eller interaktion med kundesupport.
    -   **Konkurrentanalyse og arbejdsrelaterede data**. Få alle mulige oplysninger om kunden (for eksempel kan være utilgængelige eller svært at spore).
-   Brug prioritet til valg af drev funktion. Det betyder, at øget beslutning træet modellen er altid en love fremgangsmåde.  

Brug af disse fire kategorier opretter illusionen, som en simpel *deterministisk* tilgang baseret på indeks, der er oprettet på begrundet faktorer hver kategori, skal tilstrækkeligt til at identificere kunder risiko for transportspand. Selvom denne opdelingen ser troværdige, er det desværre en falsk forståelse. Det skyldes, at transportspand er en tidsmæssig effekt, og de faktorer, der bidrager for at churn er som regel i midlertidige tilstand. Hvad fører en kunde, kan du overveje at forlade i dag kan være forskellige i morgen, og det selvfølgelig vil være forskellige seks måneder fra nu. Derfor en *probabilistic* model, der er en behovet.  

Denne vigtig bemærkning overset ofte i business, som normalt foretrækker et business intelligence-rundt metode til analytics, men ellers primært, fordi det er en nemmere sælge og giver adgang enkle automatisering.  

Løftet om selvbetjening analyser ved hjælp af maskine læ Studio er dog, at de fire kategorier af oplysninger, sorteres ved division eller enhed, bliver en værdifuld kilde til maskine lære mere om transportspand.  

En anden spændende ikke kommer i Azure Machine Learning er muligheden for at tilføje et brugerdefineret modul til lager over foruddefinerede moduler, der allerede findes. Denne egenskab, opretter grundlæggende, mulighed for at vælge biblioteker og oprette skabeloner til lodret markeder. Det er et vigtigt håndfuld af Azure Machine Learning på markedet.  

Vi håber at fortsætte i dette emne i fremtiden, især vedrører stor data analyser.
  
##<a name="conclusion"></a>Konklusion
Dette dokument beskrives en fornuftig fremgangsmåde til at løse almindelige problemet med kunde transportspand ved hjælp af en generisk ramme. Vi betragtes som en prototyper resultatværdier modeller og implementeret ved hjælp af Azure Machine Learning. Til sidst skal vurderes vi præcision og ydeevne prototyper løsningens med hensyn til tilsvarende algoritmer i SAS.  

**Du kan finde flere oplysninger:**  

Dette dokument, der hjælpe dig med at? Skal du give os feedback. Fortæl os på en skala fra 1 (dårlig) til 5 (fremragende), hvordan vil du vurdere dette dokument, og hvorfor har du givet det denne bedømmelse? Eksempel:  

-   Er du vurdering af det høj på grund af har gode eksempler, fremragende skærmbilleder, fjern markeringen ved at skrive, eller en anden mulig grund?
-   Er du vurdering af det lav på grund af dårlig eksempler, uskarpt skærmbilleder eller uklare skriver?  

Denne feedback hjælper os med at forbedre kvaliteten af hvidbøger vi slip.   

[Sende feedback](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Referencer
[1] skønnet Analytics: ud over prognoser, Vesteuropa McKnight Information Management, juli/August 2011, p.18-20.  

[2] Wikipedia-artikel: [nøjagtighed](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [SKARPE-DM 1.0: trinvise Data Mining Guide](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [big Data Marketing: deltage dine kunder mere effektivt og Drive værdi](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco churn model skabelon](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) i [Cortana Intelligence galleri](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Tillæg

![][10]

*Figur 12: Øjebliksbillede af en præsentation på transportspand prototyper*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
