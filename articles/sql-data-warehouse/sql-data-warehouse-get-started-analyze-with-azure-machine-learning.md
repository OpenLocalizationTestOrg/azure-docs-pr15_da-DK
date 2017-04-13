<properties
   pageTitle="Analysere data med Azure Machine Learning | Microsoft Azure"
   description="Bruge Azure maskine læ til at oprette en skønnet machine learning-model, der er baseret på data, der er gemt i Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analysere data med Azure Machine Learning

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Dette selvstudium bruger Azure maskine læ til at opbygge en skønnet machine learning-model, der er baseret på data, der er gemt i Azure SQL Data Warehouse. Nærmere betegnet kan opbygger dette en målrettede markedsføringskampagne for Adventure Works, butik cykler ved forudsigelser, hvis en kunde sandsynligvis til at købe en cykler eller ej.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Forudsætninger
Hvis du vil gennemgå dette selvstudium, skal du:

- Et SQL Data Warehouse forudindlæst med AdventureWorksDW eksempeldata. Se [oprette et SQL Data Warehouse][] for at klargøre, og vælg at indlæse eksempeldataene. Hvis du allerede har et datawarehouse, men ikke har eksempeldata, kan du [indlæse eksempeldata manuelt][].

## <a name="1-get-data"></a>1. Hent data
Data, der er i visningen dbo.vTargetMail i AdventureWorksDW databasen. At læse disse data:

1. Log på [Azure Machine Learning studio][] , og klik på mine forsøg.
2. Klik på **+ Ny** , og vælg **Tom forsøg**.
3. Angiv et navn til din forsøg: rettet Marketing.
4. Træk modulet, der **læser** fra ruden moduler til lærredet.
5. Angiv oplysninger om databasen SQL Data Warehouse i ruden Egenskaber.
6. Angiv database **forespørgslen** for at læse dataene af interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Køre forsøget ved at klikke på **Kør** under forsøg lærredet.
![Køre forsøget][1]


Når forsøget afsluttes kører korrekt, skal du klikke outputport nederst i modulet læser og vælge **visuelle effekter** til at se de importerede data.
![Få vist importerede data][3]


## <a name="2-clean-the-data"></a>2. rydde dataene
Hvis du vil rydde dataene, slippe nogle kolonner, der ikke er relevante for modellen. Sådan gør du:

1. Træk modulet **Project kolonner** til lærredet.
2. Klik på **Start kolonne Datavælger** i ruden Egenskaber til at angive, hvilke kolonner, du ønsker at slippe.
![Project kolonner][4]

3. Udelade to kolonner: CustomerAlternateKey og GeographyKey.
![Fjern unødvendige kolonner][5]


## <a name="3-build-the-model"></a>3. opbygge modellen
Vi vil opdele dataene 80-20: 80% til at undervise en maskine, model, og hvordan 20% for at teste modellen. Vi vil gøre brug af "To-klasse" algoritmer på problemet binære klassificering.

1. Træk det **opdelte** modul til lærredet.
2. Angiv 0,8 for brøk af rækker i den første output datasæt i ruden Egenskaber.
![Opdele data i kursus og test][6]
3. Træk modulet **To klasse øget beslutningstræ** til lærredet.
4. Træk modulet **Tog Model** i lærredet, og Angiv materialer. Klik derefter på **Start kolonne Datavælger** i ruden Egenskaber.
      - Først input: ML algoritme.
      - Andet input: Data til at undervise algoritmen på.
![Oprette forbindelse modulet tog Model][7]
5. Vælg kolonnen **BikeBuyer** som kolonnen til at forudsige.
![Vælg kolonne til at forudsige][8]


## <a name="4-score-the-model"></a>4. resultat modellen
Nu vil vi teste, hvordan modellen udfører på testdata. Vi sammenligner algoritmen af vores valg med en anden algoritme at se, som udfører bedre.

1. Træk **Karakteren Model** modul til lærredet.
    Først input: uddannelse model anden input: testdata ![resultat modellen][9]
2. Træk **To klasse Bayes punkt maskine** til forsøg lærredet. Vi vil sammenligne, hvordan denne algoritme udfører sammenlignes med to klasse øget beslutningstræ.
3. Kopiere og indsætte moduler tog Model og karakteren Model i lærredet.
4. Træk modulet **Evaluere Model** til på lærredet for at sammenligne de to algoritmer.
5. **Køre** forsøget.
![Køre forsøget][10]
6. Klik på outputporten nederst i modulet evaluere Model og visuelle effekter.
![Visualisere evalueringsresultater][11]

Angivet målene er ROC kurve, præcision tilbagekaldelsen diagrammet og elevatoren kurve. Man kigger på disse målepunkter, kan vi se, at den første model udføres bedre end den anden opgave. Se på hvad den første model, der er skønsmæssigt beregnet, klik på på outputport af karakteren modellen og visuelle effekter.
![Visualisere karakteren resultater][12]

Du får vist to flere kolonner, der er føjet til dit test datasæt.

- Karakter sandsynligheder: sandsynligheden for, at en kunde er en cykler køber.
- Karakter for etiketter: klassifikation udført af modellen – cykler køber (1) eller ej (0). Denne sandsynlighed grænseværdi for etiketter er indstillet til 50% og kan tilpasses.

Sammenligne kolonnen BikeBuyer (faktisk) med de karakter navne (forudsigelse), kan du se hvor godt modellen er udført. Du kan bruge denne model til at foretage prognoser for nye kunder og udgive denne model som en webtjeneste eller skrive resultater tilbage til SQL Data Warehouse som næste trin.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om at bygge skønnet machine learning-modeller, se [Introduktion til maskine undervisning på Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduktion til Machine Learning på Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[indlæse eksempeldata manuelt]: sql-data-warehouse-load-sample-databases.md
[Oprette et SQL datawarehouse]: sql-data-warehouse-get-started-provision.md
