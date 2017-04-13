<properties 
    pageTitle="Indlæse data i lagerplads miljøer for analyser | Microsoft Azure" 
    description="Flytte Data til og fra Azure Blob-lager" 
    services="machine-learning,storage" 
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

# <a name="load-data-into-storage-environments-for-analytics"></a>Indlæse data i lagerplads miljøer for analyser

Team Data videnskabelige processen kræver, at data være optagelse eller indlæses i en række forskellige lagerplads miljøer skal behandles eller analysere bedst i hvert trin i processen. Datadestinationer ofte anvendte i behandling omfatter Azure Blob-lager, SQL Azure-databaser, SQL Server på Azure VM, HDInsight (Hadoop) og Azure Machine Learning. 

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

I denne **menu** links til emner, der beskriver, hvordan indtager data til disse mål-miljøer, hvor dataene gemmes og behandles.

Tekniske og forretningsbehov samt den oprindelige placering, formatere og størrelsen på dine data, der bestemmer de destinationsadresse-miljøer, hvor dataene skal være optagelse for at opnå formålet med din analyse. Det er ikke usædvanlige for et scenarie med til at kræve data, der skal flyttes på tværs af flere miljøer at opnå forskellige opgaver, der kræves til at opbygge en skønnet model. Denne række af opgaver kan omfatte, for eksempel undersøgelse af data, forbehandling, rydning af unødvendig, ned-udvalg, og model kursus.
