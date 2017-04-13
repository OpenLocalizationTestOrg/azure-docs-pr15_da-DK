<properties
    pageTitle="Flytte Data til og fra Azure Blob-lager | Microsoft Azure"
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
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>Flytte Data til og fra Azure Blob-lager

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Vejledning i teknologier, der bruges til at flytte data til eller fra Azure Blob-lager er sammenkædet her:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
Hvilken metode er bedst for dig, afhænger af scenariet. [Scenarier for avancerede analyser i Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md) artiklen hjælper dig med at finde ud af de ressourcer, du har brug for en række data videnskabelige arbejdsprocesser, der bruges i avanceret analyse processen.

> [AZURE.NOTE] Henvise til [Azure Blob grundlæggende](../storage/storage-dotnet-how-to-use-blobs.md) og til [Azure Blob-tjenesten](https://msdn.microsoft.com/library/azure/dd179376.aspx)for at få en komplet Introduktion til Azure blob-lager.

Som et alternativ kan du bruge [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) til: 

- oprette og planlægge en rørledning, der henter data fra Azure blob-lager 
- overføre den til en publiceret Azure Machine Learning-webtjeneste 
- modtage resultaterne skønnet analyser og 
- overføre resultaterne til lagerplads. 

Se [oprette skønnet rørledninger ved hjælp af Azure Data Factory og Azure Machine Learning](../data-factory/data-factory-azure-ml-batch-execution-activity.md)kan finde flere oplysninger.

## <a name="prerequisites"></a>Forudsætninger

Dette dokument forudsætter, at du har et Azure-abonnement, en lagerplads konto og tasten lagerplads for den pågældende konto. Før du overfører/henter data, skal du kende Azure lagerplads konto navn og konto nøglen.

- For at konfigurere et Azure abonnement skal du se [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).
- Vejledning i at oprette en lagerplads konto og for at komme konto og vigtige oplysninger, skal du se [om Azure lagerplads konti](../storage/storage-create-storage-account.md).
