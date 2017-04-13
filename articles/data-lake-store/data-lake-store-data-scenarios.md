<properties 
   pageTitle="Data scenarier, der vedrører sø datalager | Microsoft Azure" 
   description="Forstå de forskellige scenarier og værktøjer, ved hjælp af, hvilke data kan optagelse, behandles, hentes og visualized i et datalager sø" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Brug af Azure sø datalager for stor datakrav

Der er fire vigtige faser i stor databehandling:

* Ingesting store mængder data i et datalager, i realtid eller i batches
* Behandling af data
* Hente data
* Visualisere data

I denne artikel, kan vi se på disse tre faser i forhold til Azure sø datalager at forstå de indstillinger og værktøjer til dine behov stor data.

## <a name="ingest-data-into-data-lake-store"></a>Indtager data til sø datalager

Dette afsnit fremhæver de forskellige kilder af data og de forskellige måder, som kan være optagelse disse data til en sø datalager-konto.

![Ingest data til sø datalager] (./media/data-lake-store-data-scenarios/ingest-data.png "Ingest data til sø datalager")

### <a name="ad-hoc-data"></a>Ad hoc-data

Dette repræsenterer mindre datasæt, der bruges til prototyper et stor data-program. Der er forskellige måder at ingesting ad hoc-data afhængigt af kilden til dataene.

| Datakilde        | Indtager den ved hjælp af                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Lokale computer     | <ul> <li>[Azure-portalen](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure på tværs af platforme CLI](data-lake-store-get-started-cli.md)</li> <li>[Brug af Data sø Tools til Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure-lager Blob | <ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[AdlCopy værktøj](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp, der kører på HDInsight klynge](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Streamet data

Dette repræsenterer data, der kan oprettes fra forskellige kilder som programmer, enheder, sensorer osv. Disse data kan være optagelse i et datalager sø af forskellige værktøjer. Disse værktøjer, som regel registrere og behandle dataene på grundlag af en begivenhed ved begivenhed i realtid, og skriv derefter begivenheder i batches til sø datalager så de kan udføres yderligere. 

Følgende er værktøjer, som du kan bruge:
 
* [Azure Stream Analytics] (.. / stream-analytics-data-sø-output) - begivenheder optagelse i begivenhed Hubs kan skrives til Azure Data sø ved hjælp af en Azure sø datalager output.
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md) - du kan skrive data direkte til sø datalager fra Storm klynge.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – du kan modtage hændelser fra begivenhed Hubs og derefter skrive den i sø datalager ved hjælp af [Data sø Store .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relationelle data

Du kan også kildedata fra relationsdatabaser. Relationsdatabaser indsamle store mængder data, som kan give vigtige viden, hvis behandlet via en stor data rørledning over en tidsperiode. Du kan bruge følgende værktøjer til at flytte sådanne data til sø datalager.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web server logdata (upload ved hjælp af brugerdefinerede programmer)

Denne type datasæt er specifikt fremhævet, fordi analyse web serverdata i en fælles use case for stor data-programmer og kræver store mængder logfiler overføres til sø datalager. Du kan bruge en af følgende værktøjer til at skrive dine egne scripts eller programmer til at overføre sådanne data.

* [Azure på tværs af platforme CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure datalager sø .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

Web server logdata til overførsel, og også for overførsel af andre typer data (fx sociale sentiments data), er det en god metode til at skrive dine egne brugerdefinerede scripts/programmer, fordi det giver dig mulighed for at medtage dataene uploade komponent som en del af dit større stor dataprogram. I nogle tilfælde kan denne kode tage form af et script eller et enkelt kommandolinjen til. I andre tilfælde kan koden bruges til at integrere stor databehandling med en forretningsprogram eller løsning.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Data, der er knyttet til Azure HDInsight klynger

De fleste HDInsight klynge typer (Hadoop, HBase, Storm) understøtter sø datalager som et lager til lagring af data. HDInsight klynger adgang til data fra Azure lagerplads BLOB (WASB). For at forbedre ydeevnen, kan du kopiere dataene fra WASB til en sø datalager-konto, der er knyttet til klyngen. Du kan bruge følgende værktøjer til at kopiere dataene.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Data gemt i lokalt eller IaaS Hadoop klynger

Store mængder data kan blive gemt i eksisterende Hadoop klynger lokalt på maskiner ved hjælp af HDFS. Hadoop-klynger kan være i en lokal installation eller muligvis i en IaaS klynge på Azure. Der kan være krav til at kopiere disse data til Azure sø datalager, til en manuel fremgangsmåde eller i en tilbagevendende måde. Der er forskellige indstillinger, du kan bruge til at opnå dette. Nedenfor vises en liste over alternativer og de tilknyttede kompromiser.

| Hvordan du håndterer det  | Detaljer | Fordele   | Overvejelser i forbindelse med  |
|-----------|---------|--------------|-----------------|
| Brug Azure Data Factory (ADF) til at kopiere data direkte fra Hadoop klynger til Azure sø datalager | [ADF understøtter HDFS som datakilde](../data-factory/data-factory-hdfs-connector.md) | ADF understøtter out box til HDFS og første klasse start til slut-håndtering og overvågning | Kræver, at Datastyringsgateway installeres lokalt eller i IaaS klynge |
| Eksportere data fra Hadoop som filer. Derefter skal du kopiere filerne til Azure Data sø Store ved hjælp af hensigtsmæssig måde.                                   | Du kan kopiere filer til Azure sø datalager ved hjælp af: <ul><li>[Azure PowerShell til Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure på tværs af platforme CLI for Windows OS](data-lake-store-get-started-cli.md)</li><li>Brugerdefineret app ved hjælp af en hvilken som helst Data sø Store SDK</li></ul> | Hurtig at komme i gang. Kan gøre tilpassede overførsler                                                   | Proces med flere trin, der involverer flere teknologier. Administration og overvågning vokser til være en udfordring over tid, der er tilpasset karakter af værktøjer |
| Brug Distcp til at kopiere data fra Hadoop til Azure-lager. Derefter skal du kopiere data fra Azure-lager til datalager sø ved hjælp af hensigtsmæssig måde. | Du kan kopiere data fra Azure-lager til sø datalager ved hjælp af: <ul><li>[Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)</li><li>[AdlCopy værktøj](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp kører på HDInsight klynger](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| Du kan bruge open source-værktøjer. | Proces med flere trin, der involverer flere teknologier |

### <a name="really-large-datasets"></a>Virkelig store datasæt

Til overførsel datasæt, der strækker i flere TB, kan ved hjælp af metoderne er beskrevet ovenfor være tidspunkter langsom og dyrt. Du kan bruge indstillingerne herunder i så fald.

* **Brug af Azure ExpressRoute**. Azure ExpressRoute gør det muligt at oprette private forbindelser mellem Azure datacentre og infrastruktur i din virksomhed. Dette giver en pålidelig indstilling til at overføre store datamængder. Få mere at vide dokumentationen [Azure ExpressRoute](../expressroute/expressroute-introduction.md).


* **"Offline" overføre data**. Hvis ved hjælp af Azure ExpressRoute ikke er muligt for en eller anden grund, kan du bruge [Azure Importér/Eksportér tjeneste](../storage/storage-import-export-service.md) til at levere harddiskdrev med dine data til et Azure datacenter. Dine data først er overført til Azure-lager BLOB. Du kan derefter bruge [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) eller [AdlCopy værktøj](data-lake-store-copy-data-azure-storage-blob.md) til at kopiere data fra Azure-lager BLOB til sø datalager.

    >[AZURE.NOTE] Mens ved hjælp af tjenesten Importér/Eksportér filstørrelser på den disk, der leveres til Azure datacenter ikke må være større end 200 GB.


## <a name="process-data-stored-in-data-lake-store"></a>Behandle data, der er gemt i sø datalager

Når dataene er tilgængelig i sø datalager kan du køre analyse af dataene ved hjælp af de understøttede store dataprogrammer. I øjeblikket, kan du bruge Azure HDInsight og Azure Data sø analyse til at køre data analysis job på de data, der er gemt i sø datalager. 

![Analysér data i sø datalager] (./media/data-lake-store-data-scenarios/analyze-data.png "Analysér data i sø datalager")

Du kan se eksemplerne nedenfor.

* [Oprette en HDInsight klynge med sø datalager som lager](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Hente data fra sø datalager

Du kan også vil hente eller flytte data fra Azure sø datalager for scenarier f.eks.:

* Flytte data til andre typer lagre til brugergrænsefladen med dine eksisterende databehandling rørledninger. Du kan for eksempel vil flytte data fra sø datalager til Azure SQL-Database eller en lokal SQL Server.
* Hente data til din lokale computer til behandling i ide-miljøer bygge programmet prototyper.

![Udgangspunkt data fra sø datalager] (./media/data-lake-store-data-scenarios/egress-data.png "Udgangspunkt data fra sø datalager")

I så fald kan du bruge en af følgende indstillinger:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Du kan også bruge følgende metoder til at skrive dit eget script program for at hente data fra sø datalager.

* [Azure på tværs af platforme CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure datalager sø .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualisere data i sø datalager

Du kan bruge en blanding af tjenester til at oprette visuelle repræsentationer af data, der er gemt i sø datalager.

![Visuelle effekter data i sø datalager] (./media/data-lake-store-data-scenarios/visualize-data.png "Visuelle effekter data i sø datalager")

* Du kan starte ved hjælp af [Azure Data Factory til at flytte data fra sø datalager til Azure SQL Data Warehouse](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Derefter kan du [integrere Power BI med Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) for at oprette visuelle repræsentation af dataene.
