<properties
   pageTitle="Bruge Azure Stream Analytics med SQL datawarehouse | Microsoft Azure"
   description="Tip til brug af Azure Stream Analytics med Azure SQL Data Warehouse til udvikling af løsninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Bruge Azure Stream Analytics med SQL datawarehouse

Azure Stream Analytics er en komplet administreret tjeneste give lav ventetid, høj tilgængelighed, SVG komplekse begivenhed behandling over streaming data i skyen. Du kan lære de grundlæggende funktioner ved at læse [Introduktion til Azure værdistrøm analyser][]. Derefter kan du læse hvordan du opretter en til en komplet løsning med Stream Analytics ved at følge i [gang med at bruge Azure Stream Analytics][] selvstudium.

I denne artikel lærer du, hvordan du bruger databasen Azure SQL Data Warehouse som en output sink til din damp Analytics-job.

## <a name="prerequisites"></a>Forudsætninger

Først skal køre gennem følgende trin i [gang med at bruge Azure Stream Analytics][] -selvstudium.  

1. Oprette en begivenhed Hub input
2. Konfigurere og starte begivenhed generator program
3. Klargør et Stream Analytics-job
4. Angiv job input og forespørgsel

Oprette en Data Warehouse Azure SQL-database

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Angive job output: Azure SQL Data Warehouse database

### <a name="step-1"></a>Trin 1

Klik på **OUTPUT** fra toppen af siden i Stream Analytics tingene, og klik derefter på **Tilføj OUTPUT**.

### <a name="step-2"></a>Trin 2

Vælg SQL-Database, og klik på Næste.

![][add-output]

### <a name="step-3"></a>Trin 3
Angiv følgende værdier på den næste side:

- *Output Alias*: Angiv et fuldt navn til denne job output.
- *Abonnement*:
    - Hvis databasen SQL Data Warehouse er i samme abonnement som værdistrøm Analytics jobbet, kan du vælge Brug SQL-Database fra nuværende abonnement.
    - Hvis databasen er i et andet abonnement, skal du vælge Brug SQL-Database fra et andet abonnement.
- *Database*: Angiv navnet på en destinationsdatabasen.
- *Servernavn*: angive servernavnet for den database, du lige har angivet. Du kan bruge Azure klassisk Portal for at finde dette.

![][server-name]

- *Brugernavn*: Angiv navnet på en konto, der har skrivetilladelser til databasen.
- *Adgangskode*: angive adgangskoden for den angivne brugerkonto.
- *Tabel*: Angiv navnet på destinationstabellen i databasen.

![][add-database]

### <a name="step-4"></a>Trin 4

Klik på knappen Kontrollér, at føje dette job output og Kontrollér, at Stream Analytics kan oprette forbindelse til databasen.

![][test-connection]

Når forbindelsen til databasen er oprettet, får du vist en meddelelse nederst i portalen. Du kan klikke på Test forbindelse nederst til at teste forbindelsen til databasen.

## <a name="next-steps"></a>Næste trin

Se [Oversigt over integration af SQL Data Warehouse][]for en oversigt over integration.

Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduktion til Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Introduktion til brug af Azure Stream Analytics]: ../stream-analytics/stream-analytics-get-started.md
[Oversigt over SQL Data Warehouse udvikling]:  ./sql-data-warehouse-overview-develop.md
[Oversigt over integration af SQL Data Warehouse]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
