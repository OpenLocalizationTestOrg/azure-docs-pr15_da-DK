<properties
    pageTitle="Data videnskabelige virtuelle maskiner i Azure | Microsoft Azure"
    description="Angive op en Data videnskabelige virtuel maskine"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Data videnskabelige virtuelle maskiner i Azure

Instruktioner er angivet her, der beskriver, hvordan du konfigurerer en Azure VM og en Azure VM med SQL-tjenesten som IPython notesbog-servere. Virtuelt Windows er konfigureret med understøtter funktioner som IPython notesbog, Azure Storage Explorer og AzCopy samt andre funktioner, der er praktiske, når data videnskabelige projekter. Azure-lager Explorer og AzCopy, for eksempel indeholder praktiske funktioner til at overføre data til Azure-lager fra din lokale computer eller for at hente den til din lokale computer fra lagerplads. 

I denne menuhyperlinks til emner, der beskriver, hvordan du konfigurerer de forskellige data videnskabelige miljøer, der er brugt af [Team Data videnskabelige proces (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Flere typer Azure virtuelle maskiner kan klargjort og konfigureret til at bruges som en del af et skybaseret data videnskabelige miljø. Beslutning om, hvilken version af virtuelt bruge afhænger af den type og mængden af data for at være formateret med maskine læring og destinationen for dataene i skyen. 

* Du kan finde en vejledning i spørgsmålene, du skal overveje, når du foretager denne beslutning, [Planlægge din Azure Machine Learning Data videnskabelige miljø](machine-learning-data-science-plan-your-environment.md). 
* Se [scenarier, hvor den avancerede Analytics-proces og teknologi i Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md) til et katalog over nogle af de scenarier, du kan støde på, når du udfører avanceret analyse

Der findes to instruktionssæt:

* [Konfigurere en Azure virtuel maskine, som en server til IPython notesbog til avanceret analyse](machine-learning-data-science-setup-virtual-machine.md) viser, hvordan du klargør en Azure virtuelt med IPython notesbog og andre værktøjer, der bruges til at gøre data videnskabelige i de tilfælde, hvor en formular Azure-lager end SQL kan bruges til at lagre dataene.

* [Konfigurere en virtuel maskine Azure SQL Server, som en server til IPython notesbog til avanceret analyse](machine-learning-data-science-setup-sql-server-virtual-machine.md) viser, hvordan du klargør et virtuelt Azure SQL Server med IPython notesbog og andre værktøjer, der bruges til at gøre data videnskabelige i de tilfælde, hvor en SQL-database kan bruges til at lagre dataene.

Når klargjort og konfigureret, vil disse virtuelle maskiner, er klar til brug som IPython notesbog servere for udforske og behandling af data samt andre opgaver, der skal bruges sammen med Azure maskine læring og Team Data videnskabelige proces (TDSP). De næste trin i processen data videnskabelige er tilknyttet i [TDSP læringssti](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) og kan indeholde trin, flytte data til SQL Server- eller HDInsight, proces, og lyt det der forberedelse til learning fra dataene med Azure maskine læ.


> [AZURE.NOTE] Azure virtuelle computere er prisen som **betale for kun at du bruger**. For at sikre, at du ikke er blive faktureret når du ikke bruger din virtuelle maskine, skal den være i tilstanden **stoppet (Deallocated)** fra [Azure klassisk Portal](http://manage.windowsazure.com/). Finde en trinvis vejledning og Sådan deallokere du virtuelt i [lukning og deallokere virtuelt når den ikke er i brug](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
