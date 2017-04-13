<properties 
    pageTitle="Indlæse TB data i SQL Data Warehouse | Microsoft Azure" 
    description="Viser, hvordan 1 TB data kan indlæses i Azure SQL Data Warehouse under 15 minutter med Azure Data Factory" 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="jingwang"/>

# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-azure-data-factory-copy-wizard"></a>Indlæse 1 TB i Azure SQL Data Warehouse under 15 minutter med Azure Data Factory [kopi Wizard]
[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) er en skybaseret skala op-database kan behandle store mængder data, både relationelle og ikke-relationelle.  Bygget på stort omfang parallel behandling (MPP) arkitektur er SQL Data Warehouse optimeret til enterprise data warehouse arbejdsmængder.  Det giver adgang til skyen Elasticitet med fleksibilitet til at skalere lager og beregne uafhængigt af hinanden.

Introduktion til Azure SQL Data Warehouse er nu nemmere end nogensinde ved hjælp af **Azure Data Factory**.  Azure Data Factory er en fuldt administreret skybaseret data integration tjeneste, som kan bruges til at udfylde et SQL Data Warehouse med data fra dit eksisterende system og gemme du værdifuld tid under evaluering af SQL Data Warehouse og opbygning af din analytics løsninger ovenpå.  Her er de vigtigste fordele ved indlæsning af data til Azure SQL Data Warehouse ved hjælp af Azure Data Factory:

- **Let at konfigurere**: 5 trin intuitiv guiden med ingen scripting påkrævet.
- **Omfattende data gemme support**: indbygget understøttelse af et bredt udvalg af lokale og skybaserede data butikker.
- **Sikker og kompatibel**: data er overført via HTTPS eller ExpressRoute samt globale service tilstedeværelse sikrer, at dine data forlader aldrig i geografisk rammen
- **Ubegrænset ydeevne ved hjælp af PolyBase** – ved hjælp af Polybase er den mest effektive måde at flytte data til Azure SQL Data Warehouse. Brug af funktionen midlertidige blob, kan du opnå høj belastning hastigheder fra alle typer data butikker ud over Azure Blob-lager, som Polybase understøtter som standard.

Denne artikel beskrives, hvordan du bruger guiden Data Factory kopi til at indlæse 1 TB data fra Azure Blob-lager til Azure SQL Data Warehouse på under 15 minutter på over 1,2 GBps overførsel.

I denne artikel indeholder en trinvis vejledning til at flytte data til Azure SQL Data Warehouse ved hjælp af guiden kopi. 

> [AZURE.NOTE] Se [flytte data til og fra Azure SQL Data Warehouse ved hjælp af Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artikel generelle oplysninger om funktioner af Data Factory i flytning af data til/fra Azure SQL Data Warehouse. 
> 
> Du kan også oprette rørledninger ved hjælp af Azure portalen, Visual Studio, PowerShell, osv. Se [Selvstudium: kopiere data fra Azure Blob til Azure SQL-Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) for en hurtig gennemgang med en trinvis vejledning til brug af den kopi aktivitet i Azure Data Factory.  

## <a name="prerequisites"></a>Forudsætninger
- Azure Blob-lager: dette forsøg bruger Azure Blob Storage (GRS) til lagring af TPC-H test datasæt.  Hvis du ikke har en Azure-lager-konto, se, [hvordan du opretter en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account).
- [TPC-H](http://www.tpc.org/tpch/) data: vi bruge TPC-H som test datasættet.  For at gøre det, du skal bruge `dbgen` fra TPC-H værktøjskassen, som hjælper dig med at generere datasættet.  Du kan enten hente kildekode for `dbgen` [TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) værktøjer og samle det selv, eller hente kompileret binær fra [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Køre dbgen.exe med følgende kommandoer til at generere 1 TB flad fil til `lineitem` tabellen opslag på tværs af 10 filer:
    - `Dbgen -s 1000 -S **1** -C 10 -T L -v`
    - `Dbgen -s 1000 -S **2** -C 10 -T L -v`
    - …
    - `Dbgen -s 1000 -S **10** -C 10 -T L -v` 

    Kopiere nu genererede filer til Azure Blob.  Refererer til [flytte data til og fra et lokalt filsystem ved hjælp af Azure Data Factory](data-factory-onprem-file-system-connector.md) til, hvordan du gør dette ved hjælp af ADF kopier.   
- Azure SQL datawarehouse: dette forsøg indlæser data i Azure SQL-datalager, der er oprettet med 6.000 DWUs

    Se [oprette en Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision/) detaljerede oplysninger om hvordan du opretter en SQL Data Warehouse database.  For at få den bedste mulige Indlæs ydeevne i SQL Data Warehouse ved hjælp af Polybase, Vælg det maksimale antal Data Warehouse enheder (DWUs) tilladt i indstillingen ydeevne, som er 6.000 DWUs.

    > [AZURE.NOTE] 
    > Under indlæsning af fra Azure Blob, er de data, der indlæser ydeevne direkte proportional med antallet af DWUs du konfigurerer på SQL Data Warehouse:
    > 
    > Indlæse 1 TB i 1.000 DWU SQL Data Warehouse tager 87min (~ 200MBps overførselshastighed) indlæse 1 TB i 2.000 DWU SQL Data Warehouse tager 46min (~ 380MBps overførselshastighed) indlæse 1 TB i 6.000 DWU SQL Data Warehouse tager 14min (~1.2GBps overførsel) 

    Hvis du vil oprette et SQL Data Warehouse med 6.000 DWUs, skal du flytte skyderen ydeevne helt til højre:

    ![Ydeevnen skyderen](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    For en eksisterende database, der ikke er konfigureret med 6.000 DWUs, kan du skalere det op ved hjælp af Azure portal.  Gå til databasen i Azure-portalen, og der er ikke en **skala** -knap i panelet **Oversigt** , der er vist på følgende billede:

    ![Knappen skala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klik på knappen **skala** for at åbne panelet følgende Flyt skyderen til den største værdi, og klik på knappen **Gem** .

    ![Dialogboksen skala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)
    
    Dette forsøg indlæser data til Azure SQL Data Warehouse ved hjælp af `xlargerc` ressourceklasse.

    For at opnå bedste mulige overførselshastighed, Kopiér skal udføres ved hjælp af en SQL Data Warehouse bruger, der hører til `xlargerc` ressourceklasse.  Lær at gøre det ved følgende [ændre en bruger ressource klasse eksempel](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).  

- Oprette destination tabelskema i Azure SQL Data Warehouse database, ved at køre følgende DDL-sætning:

        CREATE TABLE [dbo].[lineitem]
        (
            [L_ORDERKEY] [bigint] NOT NULL,
            [L_PARTKEY] [bigint] NOT NULL,
            [L_SUPPKEY] [bigint] NOT NULL,
            [L_LINENUMBER] [int] NOT NULL,
            [L_QUANTITY] [decimal](15, 2) NULL,
            [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
            [L_DISCOUNT] [decimal](15, 2) NULL,
            [L_TAX] [decimal](15, 2) NULL,
            [L_RETURNFLAG] [char](1) NULL,
            [L_LINESTATUS] [char](1) NULL,
            [L_SHIPDATE] [date] NULL,
            [L_COMMITDATE] [date] NULL,
            [L_RECEIPTDATE] [date] NULL,
            [L_SHIPINSTRUCT] [char](25) NULL,
            [L_SHIPMODE] [char](10) NULL,
            [L_COMMENT] [varchar](44) NULL
        )
        WITH
        (
            DISTRIBUTION = ROUND_ROBIN,
            CLUSTERED COLUMNSTORE INDEX
        )

Med de nødvendige trin, der er fuldført, er vi nu klar til at konfigurere den kopi aktivitet ved hjælp af guiden kopi.

## <a name="launch-copy-wizard"></a>Starte guiden til kopi

1.  Log på [Azure-portalen](https://portal.azure.com).
2.  Klik på **+ Ny** fra den øverste venstre hjørne, skal du klikke på **Intelligence + analytics**, og på **Data Factory**. 
6. I de **nye data factory** blade:
    1. Angiv **LoadIntoSQLDWDataFactory** for **navn**.
        Navnet på Azure data factory skal være globalt entydige. Hvis du får fejl: **Data factory navnet "LoadIntoSQLDWDataFactory" er ikke tilgængelig**, ændre navnet på data factory (for eksempel yournameLoadIntoSQLDWDataFactory) og prøve at oprette igen. Se emnet med [Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler for Data Factory elementer.  
     
    2. Vælg dit Azure **abonnement**.
    3. Til ressourcegruppe, du gør et af følgende trin: 
        1. Vælg **Brug eksisterende** til at vælge en eksisterende ressourcegruppe.
        2. Vælg **Opret ny** for at angive et navn til en ressourcegruppe.
    3. Vælg en **placering** til data factory.
    4. Markér afkrydsningsfeltet **Fastgør til dashboard** i nederst del af bladet.  
    5. Klik på **Opret**.
10. Efter at oprettelsen er fuldført, skal se du bladet **Data Factory** , som vist på følgende billede:

    ![Data factory-startsiden](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
11. Klik på feltet **kopiere data** for at starte **Guiden til kopi**på hjemmesiden Data Factory. 

    > [AZURE.NOTE] Hvis du ser, at webbrowseren er fastlåst ved "Godkendelse af...", Deaktiver/Fjern markeringen af indstillingen **Bloker tredjepartscookies og webstedsdata** (eller) beholde den aktiveret og oprette en undtagelse for **login.microsoftonline.com** og prøv derefter at åbne guiden igen.


## <a name="step-1-configure-data-loading-schedule"></a>Trin 1: Konfigurere indlæsning tidsplan af data
Det første trin er at konfigurere de data, der indlæser tidsplan.  

På siden **Egenskaber** :
1. Angiv **CopyFromBlobToAzureSqlDataWarehouse** for **Opgavenavn**
2. Vælg **Kør én gang nu** indstilling.   
3. Klik på **Næste**.  

![Kopiere guiden - side med egenskaber](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Trin 2: Konfigurer kilde
I dette afsnit beskrives disse trin for at konfigurere kilden: Azure Blob, der indeholder linjen 1 TB TPC-H elementet filer.

Vælg **Azure Blob-lager** , mens dataene gemmes og klikke på **Næste**.

![Kopiere guiden – Vælg kildesiden](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

Udfyld forbindelsesoplysningerne for kontoen Azure Blob-lager, og klik på **Næste**.

![Kopiere guiden – datakildens forbindelsesoplysninger](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

Vælg den **mappe** , der indeholder TPC-H linjeelementer filerne, og klik på **Næste**.

![Kopiere guiden – Vælg en mappe](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

Hvis du klikker på **Næste**, registreres formateringsindstillinger filer automatisk.  Kontrollér, at kontrollere afgrænser denne kolonne er ' | 'i stedet for standard-komma','.  Klik på **Næste** , når du har kontrolleret dataene.

![Kopiere guiden - indstillinger for format](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Trin 3: Konfigurere destination
Dette afsnit beskrives, hvordan du konfigurerer destinationen: `lineitem` tabel i Data Warehouse Azure SQL-databasen.

Vælg **Azure SQL Data Warehouse** som destinationslageret, og klik på **Næste**.

![Kopiere guiden – Vælg destination datalager](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

Udfyld forbindelsesoplysningerne for Azure SQL Data Warehouse.  Sørg for, at du angiver den bruger, der er medlem af rollen `xlargerc` (se afsnittet **forudsætninger** for at få detaljerede anvisninger), og klik på **Næste**. 

![Kopiere guiden - destination forbindelsesoplysninger](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

Vælg destinationstabellen, og klik på **Næste**.

![Kopiere guiden – tabel tilknytning side](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

Accepter standardindstillingerne for kolonnetilknytning, og klik på **Næste**.

![Kopiere guiden - skema tilknytning side](media/data-factory-load-sql-data-warehouse/schema-mapping.png)


## <a name="step-4-performance-settings"></a>Trin 4: Ydeevne indstillinger

**Tillad polybase** er markeret som standard.  Klik på **Næste**.

![Kopiere guiden - skema tilknytning side](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Trin 5: Installere og overvåge Indlæs resultater
Klik på knappen **Udfør** skal installeres. 

![Kopiere guiden – siden Oversigt](media/data-factory-load-sql-data-warehouse/summary-page.png)

Når installationen er fuldført, skal du klikke på `Click here to monitor copy pipeline` til at overvåge kopien køre status.

Vælg den kopi rørledning, du har oprettet på listen **Aktivitet i Windows** .

![Kopiere guiden – siden Oversigt](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

Du kan få vist kopien køre detaljer i **Aktivitet vinduet Explorer** i højre panel, herunder data lydstyrken læses fra kilde og skrives i destination, varighed og den gennemsnitlige overførsel for Kør.

Som du kan se følgende skærmbillede, tog kopiere 1 TB fra Azure Blob-lager til SQL Data Warehouse 14 minutter, effektivt at nå 1.22 GBps overførsel!

![Kopiere guiden – lykkedes dialogboksen](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Bedste fremgangsmåder
Her er nogle få bedste fremgangsmåder til at køre Azure SQL Data Warehouse databasen:

- Brug en større ressourceklasse, når indlæsning til et GRUPPERET COLUMNSTORE indeks.
- Overvej at bruge hash fordeling Vælg Kolonnevis i stedet for standard round robin fordeling for mere effektiv joinforbindelser.
- Hurtigere indlæsning hastigheder, kan du overveje at bruge heap til midlertidige data.
- Oprette statistik, når du er færdig med at overføre Azure SQL Data Warehouse.

Du kan finde oplysninger i [bedste fremgangsmåder til Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md) . 

## <a name="next-steps"></a>Næste trin
- [Guiden data Factory kopi](data-factory-copy-wizard.md) - i denne artikel indeholder oplysninger om guiden kopi. 
- [Kopiér aktivitet ydeevne og justering vejledning](data-factory-copy-activity-performance.md) - i denne artikel indeholder reference ydeevnemålinger og justering vejledning.

