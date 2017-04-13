<properties 
    pageTitle="Flytte Data til og fra Azure Blob-lager med Azure lagerplads Stifinder | Microsoft Azure" 
    description="Flytte Data til og fra Azure Blob-lager med Azure lagerplads Stifinder" 
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
    ms.date="08/31/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Flytte Data til og fra Azure Blob-lager med Azure lagerplads Stifinder

Azure-lager Explorer er et gratis værktøj fra Microsoft, der gør det muligt at arbejde med Azure-lager data i Windows, macOS og Linux. Dette emne beskrives, hvordan du kan bruge det til at overføre og hente data fra Azure blob-lager. Værktøjet kan hentes fra [Microsoft Azure lagerplads Explorer](http://storageexplorer.com/).

Vejledning i teknologier, der bruges til at flytte data til eller fra Azure Blob-lager er sammenkædet her:
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]   

 
> [AZURE.NOTE] Hvis du bruger VM, der er konfigureret med de scripts, som leveres af [Data videnskabelige virtuelle maskiner i Azure](machine-learning-data-science-virtual-machines.md), derefter installeret Azure lagerplads Explorer allerede på VM.
 
> [AZURE.NOTE] Referere til [Azure Blob grundlæggende](../storage/storage-dotnet-how-to-use-blobs.md) og [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)for at få en komplet Introduktion til Azure blob-lager.   

## <a name="prerequisites"></a>Forudsætninger

Dette dokument forudsætter, at du har et Azure-abonnement, en lagerplads konto og tasten lagerplads for den pågældende konto. Før du overfører/henter data, skal du kende Azure lagerplads konto navn og konto nøglen. 

- For at konfigurere et Azure abonnement skal du se [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).
- Vejledning i at oprette en lagerplads konto og for at komme konto og vigtige oplysninger, skal du se [om Azure lagerplads konti](../storage/storage-create-storage-account.md). Gøre en note hurtigtast for kontoen lagerplads, som du skal bruge denne tast for at oprette forbindelse til kontoen med værktøjet Azure Storage Explorer.
- Værktøjet Azure Storage Explorer kan hentes fra [Microsoft Azure lagerplads Explorer](http://storageexplorer.com/). Accepter standardindstillingerne under installationen.


<a id="explorer"></a>
## <a name="use-azure-storage-explorer"></a>Brug Azure lagerplads Explorer 

Følgende trin dokument sådan upload/download af data ved hjælp af Azure Storage Explorer. 

1.  Start Microsoft Azure-lager Explorer.
2.  Vælg ikonet **Azure kontoindstillinger** , derefter **Tilføj en konto** for at få vist guiden **Log på din konto...** , og Angiv du legitimationsoplysninger. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  Vælg ikonet **Opret forbindelse til Azure-lager** for at få vist guiden **Opret forbindelse til Azure-lager** . ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Du kan angive hurtigtast fra kontoen Azure-lager på **Opret forbindelse til Azure-lager** guiden og derefter på **Næste**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Angive kontonavn lager i boksen **kontonavn** , og klik derefter på **Næste**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Kontoen lagerplads tilføjet skulle nu være angivet. Oprette en blob objektbeholder med en konto med lagerplads, skal du højreklikke på noden **Blob beholdere** i den pågældende konto, vælge **Oprette Blob objektbeholder**, og Angiv et navn.
7. Vælg objektbeholderen mål for at overføre data til en objektbeholder, og klik på knappen **Overfør** .![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klik på **…** til højre for feltet **filer** skal du vælge en eller flere filer til at overføre fra filsystemet, og klik på **Overfør** for at begynde at overføre filerne.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. Hente data, vælge blob i objektbeholderen tilsvarende at hente, og klik på **Hent**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


