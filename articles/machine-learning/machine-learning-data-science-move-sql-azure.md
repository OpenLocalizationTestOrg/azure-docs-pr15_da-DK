<properties 
    pageTitle="Flytte data til en Azure SQL-Database til Azure Machine Learning | Azure" 
    description="Oprette SQL-tabel og Indlæs data til SQL-tabel" 
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
    ms.date="09/14/2016"
    ms.author="bradsev" /> 

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Flytte data til en Azure SQL-Database til Azure Machine Learning

Dette emne beskrives indstillingerne til at flytte data fra flade filer (csv- eller TSV formater) eller fra data, der er gemt i en lokal SQL Server til en Azure SQL-database. Disse opgaver til at flytte data til skyen er en del af processen Team Data videnskabelige.

Efter et emne, der viser indstillingerne for at flytte data til en lokal SQL Server til at lære mere maskine, kan du se [flytte data til SQL Server på en Azure virtuel maskine](machine-learning-data-science-move-sql-server-virtual-machine.md).

**Menuen** følgende links til emner, der beskriver, hvordan indtager data til målrette miljøer, hvor dataene kan gemmes og behandles under Team Data videnskabelige proces (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Den følgende tabel opsummerer indstillingerne til at flytte data til en Azure SQL-Database.

<b>KILDE</b> |<b>DESTINATION: Azure SQL-Database</b> |
-------------- |--------------------------------|
<b>Flad fil (csv- eller TSV formateret)</b> |<a href="#bulk-insert-sql-query">Flere Indsæt SQL-forespørgsel |
<b>Lokal SQL Server</b> | 1. <a href="#export-flat-file">eksportere til en flad fil<br> 2. <a href="#insert-tables-bcp">guiden Overførsel af SQL-Database<br> 3. <a href="#db-migration">Webdatabase tilbage op og gendanne<br> 4. <a href="#adf">azure Data Factory |


## <a name="prereqs"></a>Forudsætninger
De fremgangsmåder, der er beskrevet her kræver, at du har:

* En **Azure-abonnement**. Hvis du ikke har et abonnement, kan du tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure-lager-konto**. Du bruger en konto til Azure-lager til lagring af data i dette selvstudium. Hvis du ikke har en Azure-lager-konto, kan du se artiklen [Opret en lagerplads konto](storage-create-storage-account.md#create-a-storage-account) . Når du har oprettet kontoen lagerplads, skal du hente den kontonøgle, der bruges til at få adgang til opbevaring. Få [vist, Kopiér og Regenerer lagerplads adgangstaster](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Adgang til en **Azure SQL-Database**. Hvis du skal konfigurere Azure SQL-Database, indeholder [Introduktion til Microsoft Azure SQL Database](../sql-database/sql-database-get-started.md) oplysninger om, hvordan du klargør en ny forekomst af en Azure SQL-Database.
* Installeret og konfigureret **Azure PowerShell** lokalt. Finde en vejledning, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

**Data**: overførsel processer vises ved hjælp af [NYC Taxi datasæt](http://chriswhong.com/open-data/foil_nyc_taxi/). NYC Taxi datasættet indeholder oplysninger om forretningsrejse data og markeder og er tilgængelige, som angivet dette indlæg på Azure blob-lager: [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/). Et eksempel og en beskrivelse af disse filer er angivet i [NYC Taxi rejser datasæt beskrivelse](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Du kan tilpasse de procedurer, som er beskrevet her og et sæt af dine egne data eller Følg trinnene beskrevet ved hjælp af NYC Taxi datasættet. Hvis du vil overføre NYC Taxi datasæt til lokal SQL Server-databasen, skal du følge fremgangsmåden i [Flere importere Data til SQL Server-Database](machine-learning-data-science-process-sql-walkthrough.md#dbload). Disse instruktioner er til en SQL Server på en Azure Virtual Machine, men fremgangsmåden til overførsel til lokal SQL Server er den samme.


## <a name="file-to-azure-sql-database"></a>Flytte data fra en flad fil kilde til en Azure SQL-database

Data i flade filer (csv- eller TSV formateret) kan flyttes til Azure SQL-database ved hjælp af en flere indsætte SQL-forespørgsel.

### <a name="bulk-insert-sql-query"></a>Flere Indsæt SQL-forespørgsel

Trinnene til proceduren med brug flere indsætte SQL-forespørgslen er ligner dem, der er omfattet af sektioner til at flytte data fra en flad fil kilde til SQL Server på en Azure VM. Yderligere oplysninger finder du [Flere indsætte SQL-forespørgsel](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Flytte Data fra lokal SQL Server til Azure SQL-database

Hvis kildedataene er gemt i en lokal SQL Server, er der forskellige muligheder for at flytte data til en Azure SQL-database:

1. [Eksportere til en flad fil](#export-flat-file) 
2. [Guiden Overførsel af SQL-Database](#insert-tables-bcp)
3. [Webdatabase tilbage op og gendanne](#db-migration)
4. [Azure Data Factory](#adf)

Fremgangsmåden for de første tre ligner meget de afsnittene i [flytte data til SQL Server på en Azure virtuel maskine](machine-learning-data-science-move-sql-server-virtual-machine.md) , der dækker de samme fremgangsmåder. Links til de relevante afsnit i dette emne er angivet i de følgende instruktioner.

###<a name="export-flat-file"></a>Eksportere til en flad fil

Fremgangsmåden for dette eksportere til en flad fil, der ligner dem dækket i [eksportere til en flad fil](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Guiden Overførsel af SQL-Database

Trin til brug af guiden Overflytning fra SQL-Database, der ligner dem dækket i [Guiden til overførsel af SQL-Database](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Webdatabase tilbage op og gendanne

Trin til webdatabasen sikkerhedskopiere og gendanne ligner dem dækket i [databasen, sikkerhedskopiere og gendanne](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Azure Data Factory

Fremgangsmåden til at flytte data til en Azure SQL-database med Azure Data Factory (ADF) er angivet i emnet til at [flytte data fra en lokal SQL server til SQL Azure med Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md). Dette emne viser, hvordan til at flytte data fra en lokal SQL Server-database til en Azure SQL-database via Azure Blob-lager ved hjælp af ADF. 

Overvej at bruge ADF, når data skal overføres kontinuerligt i et hybridt scenarie, der har adgang til både lokalt og skyen ressourcer, og når dataene er overført eller skal ændres, eller har føjet til den, når du bliver overflyttet forretningslogik. ADF giver mulighed for planlægningen og overvågning af job ved hjælp af enkle JSON-scripts, som administrerer flytning af data med jævne mellemrum. ADF har også andre funktioner som understøttelse af komplekse handlinger.




