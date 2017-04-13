<properties
   pageTitle="Bruge Redgate's Data Platform Studio til at lægge data i SQL Data Warehouse | Microsoft Azure"
   description="Lær at bruge Redgate's Data Platform Studio for lager scenarier med data."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Indlæse data med Redgate Data Platform Studio

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Dette selvstudium viser, hvordan du bruger [Redgate's Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) til at flytte data fra en lokal SQL Server til Azure SQL Data Warehouse. Data Platform Studio gælder den mest passende kompatibilitetsrettelser og optimeringer, så det er den hurtigste måde at komme i gang med SQL Data Warehouse.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) er brugt Microsoft-partner, der leverer forskellige SQL Server-værktøjer. Denne funktion i Data Platform Studio er gjort tilgængelig frit til både kommercielle og ikke-kommerciel brug.

## <a name="before-you-begin"></a>Inden du går i gang
### <a name="create-or-identify-resources"></a>Opret eller Identificer ressourcer

Før du starter selvstudiet, skal du have:

- **Lokal SQL Server-Database**: de data, du vil importere til SQL Data Warehouse skal komme fra en lokal SQL Server (version 2008R2 eller nyere). Data Platform Studio kan ikke importeres data direkte fra Azure SQL-Database eller tekstfiler.
- **Azure lagerplads konto**: Data Platform Studio stadier dataene i Azure Blob-lager før indlæse dem til en SQL Data Warehouse. Kontoen lagerplads skal bruge implementeringsmodel "ressourcestyring" (standard) i stedet for "Klassisk" implementeringsmodel. Hvis du ikke har en konto med lagerplads, Lær, hvordan du opretter en lagerplads konto. 
- **SQL Data Warehouse**: dette selvstudium flytter dataene fra lokal SQL Server til SQL Data Warehouse, så du skal have et datawarehouse online. Hvis du ikke allerede har et datalager, se, hvordan du opretter en Azure SQL Data Warehouse.

> [AZURE.NOTE] Ydeevnen er forbedret, hvis lagerplads kontoen og datawarehouse er oprettet i det samme område.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Trin 1: Log på Data Platform Studio med Azure-konto
Åbn din webbrowser og gå til webstedet [Data Platform Studio](https://www.dataplatformstudio.com/) . Logge på med den samme Azure-konto, du brugte til at oprette lagerplads konto og data warehouse. Hvis din mailadresse er knyttet til både en arbejds- eller skolekonto og en Microsoft-konto, skal du vælge den konto, der har adgang til dine ressourcer.

> [AZURE.NOTE] Hvis dette er første gang, du bruger Data Platform Studio, bliver du bedt om at give program tilladelse til at administrere dine Azure ressourcer.

## <a name="step-2-start-the-import-wizard"></a>Trin 2: Starte importguiden
Markere afkrydsningsfeltet Importér Azure SQL Data Warehouse linket til at starte importguiden på DPS hovedskærmen.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Trin 3: Installere Data Platform Studio Gateway
Hvis du vil oprette forbindelse til lokal SQL Server-databasen, skal du installere DPS gatewayen. Gatewayen er en agent for klient, der giver adgang til dit lokale-miljø, henter dataene, og overfører den til kontoen lagerplads. Dine data passerer aldrig gennem Redgates servere. Sådan installeres gatewayen:

1.  Klik på linket **Oprette Gateway**
2. Downloade og installere gatewayen hjælp af det medfølgende installationsprogram

![][2]

> [AZURE.NOTE] Gatewayen kan være installeret på en hvilken som helst computer med netværksadgang til SQL Server-kildedatabasen. Det giver adgang til SQL Server-databasen ved hjælp af Windows-godkendelse med legitimationsoplysningerne for den aktuelle bruger.

Når installeret, gatewaystatus ændres til tilsluttet, og du kan vælge Næste.

## <a name="step-4-identify-the-source-database"></a>Trin 4: Identificere kildedatabasen
Skriv navnet på den server, der hoster din database, og klik på **Næste**i tekstfeltet *Angiv servernavn* . Vælg den database, du vil importere data fra fra i rullemenuen.

![][3]

DPS inspicerer den valgte database til tabeller for at importere. Som standard importerer DPS alle tabellerne i databasen. Du kan Vælg eller fravælg tabeller ved at udvide linket alle tabeller. Vælg knappen Næste for at flytte fremad.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Trin 5: Vælg en lagerplads konto til at gemme dataene
DPS beder dig om en placering til fase dataene. Vælg en eksisterende konto lagerplads fra dit abonnement, og klik på **Næste**.

> [AZURE.NOTE] DPS oprettes en ny blob objektbeholder i den valgte lagerplads konto, og brug af en mappe, der er entydige for hver import.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Trin 6: Vælg et datawarehouse
Derefter skal vælge du en [Azure SQL Data Warehouse](http://aka.ms/sqldw) onlinedatabase til at importere dataene til. Når du har valgt databasen, skal du angive legitimationsoplysninger for at oprette forbindelse til databasen, og vælg **Næste**.

![][5]

> [AZURE.NOTE] DPS fletter datatabeller kilde til et datalager. DPS advarer dig, hvis tabelnavnet kræver det at overskrive eksisterende tabeller i et datalager. Du kan du kan også slette alle eksisterende objekter i et datalager ved at afkrydse Slet alle eksisterende objekter før import.

## <a name="step-7-import-the-data"></a>Trin 7: Importere data
DPS bekræfter, at du vil importere dataene. Blot klikke på knappen Start import for at starte dataimporten.

![][6]

DPS viser en visualisering, der viser status for at hente og overføre dataene fra lokal SQL Server og status for import til SQL Data Warehouse.

![][7]

Når importen er fuldført, viser DPS en oversigt over dataimporten og en Skift rapport over de kompatibilitetsrettelser, der er udført.

![][8]

## <a name="next-steps"></a>Næste trin
Hvis du vil udforske dine data i SQL Data Warehouse, du start ved at få vist:

- [Forespørgsel Azure SQL datawarehouse (Visual Studio)][]
- [Visualisere data med Power BI][]

Vil vide mere om Redgate's Data Platform Studio:

- [Besøg på DPS-hjemmesiden](http://www.dataplatformstudio.com/)
- [Se en demonstration af DPS på Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Finde en oversigt over andre måder at overføre og indlæse dine data i SQL Data Warehouse i:

- [Overføre din løsning til SQL Data Warehouse][]
- [Indlæse data til Azure SQL Data Warehouse](./sql-data-warehouse-overview-load.md)

Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Forespørgsel Azure SQL datawarehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualisere data med Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Overføre din løsning til SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md