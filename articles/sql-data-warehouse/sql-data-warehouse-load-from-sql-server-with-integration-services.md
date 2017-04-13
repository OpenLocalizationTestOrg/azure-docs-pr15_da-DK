<properties
   pageTitle="Indlæse data fra SQL Server til Azure SQL Data Warehouse (SSIS) | Microsoft Azure"
   description="Viser, hvordan du opretter en SQL Server Integration Services (SSIS) pakke for at flytte data fra en lang række datakilder til SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Indlæse data fra SQL Server til Azure SQL Data Warehouse (SSIS)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Oprette en SQL Server Integration Services (SSIS) pakke for at indlæse data fra SQL Server i Azure SQL Data Warehouse. Du kan eventuelt omstrukturering, transformere og rengoere dataene, som den passerer gennem dataflow SSIS.

I dette selvstudium skal du:

- Oprette et nyt Integration Services-projekt i Visual Studio.
- Oprette forbindelse til datakilder, herunder SQL Server (som en kilde) og SQL Data Warehouse (som destination).
- Designe en SSIS-pakke, der indlæser data fra kilden til destinationen.
- Køre SSIS-pakke for at genindlæse dataene.

Dette selvstudium bruger SQL Server som datakilde. SQL Server kan køre lokalt eller i en Azure virtuelt.

## <a name="basic-concepts"></a>Grundlæggende begreber

Pakken er måleenheden for arbejde i SSIS. Relaterede pakker grupperes i projekter. Du kan oprette projekter og design-pakker i Visual Studio med SQL Server Data Tools. Designprocessen er en visuel proces, hvori du trække og slippe komponenter fra værktøjskassen til designoverfladen, forbinde dem og angive deres egenskaber. Når du er færdig med din pakke, kan du eventuelt installere det i SQL Server til omfattende administration, overvågning og sikkerhed.

## <a name="options-for-loading-data-with-ssis"></a>Indstillinger for indlæsning af data med SSIS

SQL Server Integration Services (SSIS) er en fleksibel sæt værktøjer, der indeholder en række indstillinger til at oprette forbindelse til, og indlæse data i SQL Data Warehouse.

1. Bruge en ADO redskaber Destination til at oprette forbindelse til SQL Data Warehouse. Dette selvstudium bruger en ADO redskaber Destination, fordi den ikke har de færreste konfigurationsindstillinger.
2. Bruge en OLE DB Destination til at oprette forbindelse til SQL Data Warehouse. Denne indstilling kan giver lidt bedre ydeevne end ADO redskaber destinationen.
3. Benytte Azure Blob overføre opgaven til at gemme dataene i Azure Blob-lager. Brug derefter opgaven SSIS Udfør SQL til at starte et Polybase-script, der indlæser dataene i SQL Data Warehouse. Denne indstilling giver den bedste ydeevne af de tre indstillinger, der er angivet her. For at få den Azure Blob overføre opgave skal du hente [Microsoft SQL Server 2016 Integration Services Feature Pack til Azure][]. Hvis du vil vide mere om Polybase skal du se [PolyBase vejledning][].

## <a name="before-you-start"></a>Før du starter

Hvis du vil gennemgå dette selvstudium, skal du:

1. **SQL Server integrationsservices (SSIS)**. SSIS er en komponent i SQL Server og kræver en vurderingsversion eller en licenseret version af SQL Server. En vurderingsversion af SQL Server 2016 Preview, kan du se [SQL Server evaluering][].
2. **Visual Studio**. Den gratis Visual Studio 2015 Community Edition, kan du se [Visual Studio-Community][].
3. **SQL Server Data Tools til Visual Studio (SSDT)**. SQL Server Data Tools til Visual Studio 2015, kan du se [Hente SQL Server Data Tools (SSDT)][].
4. **Eksempeldata**. Dette selvstudium bruger eksempeldata, der er gemt i SQL Server i eksempeldatabasen AdventureWorks som kildedataene ind i SQL Data Warehouse. Eksempeldatabasen AdventureWorks, kan du se [AdventureWorks 2014 eksempler på databaser][].
5. **A SQL Data Warehouse database og tilladelser**. Dette selvstudium opretter forbindelse til en forekomst af SQL Data Warehouse og indlæser data i den. Du skal have tilladelser til at oprette en tabel og indlæse data.
6. **En firewallregel**. Du skal oprette en firewallregel for på SQL Data Warehouse med IP-adressen på din lokale computer, før du kan overføre data til SQL Data Warehouse.

## <a name="step-1-create-a-new-integration-services-project"></a>Trin 1: Oprette et nyt Integration Services-projekt

1. Start Visual Studio-2015.
2. I menuen **filer** , Vælg **Ny | Project**.
3. Gå til **installeret | Skabeloner | Business Intelligence | Integrationsservices** projekttyper.
4. Vælg **Integration Services-projekt**. Angiv værdier for **navn** og en **placering**, og klik derefter på **OK**.

Visual Studio åbnes og opretter et nyt Integration Services (SSIS) projekt. Derefter åbner Visual Studio designer for enkelt ny SSIS-pakke (Package.dtsx) i projektet. Får du vist følgende skærmbillede områder:

- I venstre side **Værktøjskasse** med SSIS komponenter.
- I midten, designoverfladen med flere faner. Du bruger typisk mindst **Kontrolflow** og fanerne **Dataflow** .
- Til højre, **Solution Explorer** og ruderne **Egenskaber** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Trin 2: Oprette grundlæggende dataflow

1. Træk en Data dataflow opgave fra værktøjskassen til midten af designoverfladen (under fanen **Kontrolflow** ).

    ![][02]

2. Dobbeltklik på Data dataflow opgaven at skifte til fanen dataflow.
3. Træk en ADO.NET-kilde fra listen andre kilder i værktøjskassen til designoverfladen. Med kilde-adapter, der stadig er markeret, kan du ændre navnet til **SQL Server datakilde** i ruden **Egenskaber** .
4. Træk en ADO.NET-Destination fra listen andre destinationer i værktøjskassen til designoverfladen under ADO.NET-kilde. Med destination-adapter, der stadig er markeret, kan du ændre navnet til **SQL DW destination** i ruden **Egenskaber** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Trin 3: Konfigurere kilde kortet

1. Dobbeltklik på kortet for at åbne **ADO.NET Kildeeditor**kilde.

    ![][03]

2. Klik på knappen **Ny** ud for listen **ADO.NET Forbindelsesstyring** til at åbne dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** og oprette forbindelsesindstillinger for SQL Server-databasen, som dette selvstudium indlæser data under fanen **Forbindelsesstyring** af **ADO.NET Kildeeditor**.

    ![][04]

3. Klik på knappen **Ny** for at åbne dialogboksen **Forbindelsesstyring** og oprette en ny dataforbindelse i dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** .

    ![][05]

4. Gøre følgende i dialogboksen **Forbindelsesstyring** .

    1. Vælg SqlClient Data-Provider til **udbyder**.
    2. Angiv navnet på SQL Server for **servernavn**.
    3. Vælg eller Angiv godkendelsesoplysninger i sektionen **logge serveren** .
    4. Vælg eksempeldatabasen AdventureWorks i afsnittet **oprette forbindelse til en database** .
    5. Klik på **Test forbindelse**.
    
        ![][06]
    
    6. Klik på **OK** for at vende tilbage til dialogboksen **Forbindelsesstyring** i den dialogboks, der oplyser om resultatet af forbindelsestesten.
    7. Klik på **OK** for at vende tilbage til dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** i dialogboksen **Forbindelsesstyring** .
 
5. Klik på **OK** for at vende tilbage til den **ADO.NET Kildeeditor**i dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** .
6. Vælg tabellen **Sales.SalesOrderDetail** i **ADO.NET Kildeeditor**, på listen **navn på tabellen eller visningen** .

    ![][07]

7. Klik på **eksempel** for at se de første 200 rækker af data i kildetabellen i dialogboksen **Vis forespørgselsresultater** .

    ![][08]

8. Klik på **Luk** at vende tilbage til den **ADO.NET Kildeeditor**i dialogboksen **Vis forespørgselsresultater** .
9. Klik på **OK** for at afslutte konfigurationen af datakilden i **ADO.NET Kildeeditor**skal.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Trin 4: Forbinde datakildens adapter til destination kortet

1. Vælg det kilde kort på designoverfladen.
2. Vælg den blå pil, der går fra kilde kortet, og træk den til destination editoren, indtil den klikker på plads.

    ![][10]

    I en typisk SSIS-pakke, skal bruge du et antal andre komponenter fra SSIS-værktøjskassen imellem kilden og destinationen omstrukturering, transformere og rense dine data, som den passerer gennem dataflow SSIS. Hvis du vil bevare i dette eksempel så enkelt som muligt, er vi at slutte kilden direkte til destinationen.

## <a name="step-5-configure-the-destination-adapter"></a>Trin 5: Konfigurere destination kortet

1. Dobbeltklik på destination kortet for at åbne **ADO.NET Destination Editor**.

    ![][11]

2. Klik på knappen **Ny** ud for listen **Forbindelsesstyring** til at åbne dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** og oprette forbindelsesindstillinger for Azure SQL Data Warehouse databasen, hvor dette selvstudium indlæser data under fanen **Forbindelsesstyring** af **ADO.NET Destination Editor**.
3. Klik på knappen **Ny** for at åbne dialogboksen **Forbindelsesstyring** og oprette en ny dataforbindelse i dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** .
4. Gøre følgende i dialogboksen **Forbindelsesstyring** .
    1. Vælg SqlClient Data-Provider til **udbyder**.
    2. Angiv navnet på SQL Data Warehouse for **servernavn**.
    3. Vælg **Brug SQL Server-godkendelse** , og Angiv godkendelsesoplysninger i sektionen **logge serveren** .
    4. Vælg en eksisterende Data Warehouse SQL-database i afsnittet **oprette forbindelse til en database** .
    5. Klik på **Test forbindelse**.
    6. Klik på **OK** for at vende tilbage til dialogboksen **Forbindelsesstyring** i den dialogboks, der oplyser om resultatet af forbindelsestesten.
    7. Klik på **OK** for at vende tilbage til dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** i dialogboksen **Forbindelsesstyring** .
5. Klik på **OK** for at vende tilbage til den **ADO.NET Destination Editor**i dialogboksen **Konfigurer ADO.NET Forbindelsesstyring** .
6. Klik på **Ny** ud for listen **Brug en tabel eller visning** for at åbne dialogboksen **Opret tabel** for at oprette en ny destinationstabel med en liste over kolonner, der svarer til kildetabellen i **ADO.NET Destination Editor**.

    ![][12a]

7. Gøre følgende i dialogboksen **Opret tabel** .

    1. Ændre navnet på destinationstabellen til **salgsordredetaljerne**.
    2. Fjerne kolonnen **rowguid** . **Entydigt id** -datatype understøttes ikke i SQL Data Warehouse.
    3. Ændre datatypen for kolonnen **LineTotal** til **penge**. Datatypen **decimal** understøttes ikke i SQL Data Warehouse. Oplysninger om understøttede datatyper, skal du se [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][].
    
        ![][12b]
    
    4. Klik på **OK** for at oprette tabellen og vende tilbage til den **ADO.NET Destination Editor**.

8. Vælg fanen **tilknytninger** til at se, hvordan kolonner i kilden er knyttet til kolonner i destinationen i **ADO.NET Destination Editor**.

    ![][13]

9. Klik på **OK** for at afslutte konfigurationen af datakilden.

## <a name="step-6-run-the-package-to-load-the-data"></a>Trin 6: Kør pakken for at genindlæse dataene

Kør pakken, ved at klikke på knappen **Start** på værktøjslinjen eller ved at vælge en af indstillingerne **køre** i menuen **fejlfinding** .

Når pakken begynder at køre, skal se du gule rundt om rullehjulet til at angive aktivitet samt antallet af rækker, der er behandlet indtil nu.

![][14]

Når pakken er afsluttet, kan du se grønne afkrydsningsmærker til at angive succes samt det samlede antal rækker med data, der er indlæst fra kilden til destinationen.

![][15]

Tillykke! Du har brugt SQL Server Integration Services korrekt for at indlæse data til Azure SQL Data Warehouse.

## <a name="next-steps"></a>Næste trin

- Lær mere om SSIS dataflow. Start her: [Dataflow][].
- Lær at udføre fejlfinding og foretage fejlfinding af din pakker direkte i designmiljøet. Start her: [Fejlfinding i forbindelse med værktøjer til udvikling af pakke][].
- Lær at implementere dine SSIS-projekter og pakker til Integration Services-serveren eller til en anden lagerplacering. Start her: [installation af projekter og pakker][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase vejledning]: https://msdn.microsoft.com/library/mt143171.aspx
[Hente SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[OPRETTE tabel (Azure SQL datawarehouse, Parallel datawarehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Dataflow]: https://msdn.microsoft.com/library/ms140080.aspx
[Fejlfindingsværktøjer til pakken udvikling]: https://msdn.microsoft.com/library/ms137625.aspx
[Installation af projekter og -pakker]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 integrationsservices indeholder Pack til Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server evaluering]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 eksempler på databaser]: https://msftdbprodsamples.codeplex.com/releases/view/125550
