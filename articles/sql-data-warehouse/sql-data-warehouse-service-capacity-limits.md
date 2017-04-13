<properties
   pageTitle="SQL Data Warehouse kapacitet grænser | Microsoft Azure"
   description="Maksimale værdier for forbindelser, databaser, tabeller og forespørgsler til SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse kapacitet grænser

De følgende tabeller indeholder de maksimale værdier, der er tilladt for forskellige komponenter i Azure SQL Data Warehouse.


## <a name="workload-management"></a>Administration af arbejdsbelastning

| Kategori            | Beskrivelse                                       | Maksimum            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Data Warehouse enheder (DWU)][]| Max DWU for et enkelt SQL Data Warehouse | 6000               |
| [Data Warehouse enheder (DWU)][]| Max DWU for en enkelt SQL-server         | 6000 som standard<br/><br/> Som standard har hver SQL server (fx myserver.database.windows.net) en DTU kvote af 45,000 som giver mulighed for op til 6000 DWU. Denne kvote er blot en safety grænse. Du kan øge din kvote ved [oprettelse af en supportbilletter][] og vælge *kvote* anmodning om filtype.  Til at beregne din DTU skal, multiplicere 7.5 med totalen DWU det er nødvendigt. Du kan se din aktuelle DTU forbrug fra bladet SQL server på portalen. Både er stoppet midlertidigt og ophævelse standsede databaser tælle mod kvoten DTU. |
| Databaseforbindelse | Samtidige åbne sessioner                          | 1024<br/><br/>Vi understøtter maksimalt 1024 aktive forbindelser, hver især kan sende anmodninger om til en SQL Data Warehouse database på samme tid. Bemærk, at der er begrænsninger på antallet af forespørgsler, der kan faktisk udføre samtidigt. Når på dokumentsammenfald grænse overskrides, går anmodningen til en intern kø, hvor det afventer behandles.|
| Databaseforbindelse | Maksimal hukommelse for forberedt sætninger            | 20 MB              |
| [Administration af arbejdsbelastning][] | Maksimalt antal samtidige forespørgsler                    | 32<br/><br/> Som standard kan SQL Data Warehouse udføre maksimalt 32 samtidige forespørgsler og køer resterende forespørgsler.<br/><br/>Niveauet på dokumentsammenfald kan mindske, når brugere er tildelt til en højere ressourceklasse, eller når SQL Data Warehouse er konfigureret med lav DWU. Nogle forespørgsler, som DMV forespørgsler er altid tilladelse til at køre.|
| [TempDB][]          | Maksimal størrelse af Tempdb                                | 399 GB per DW100. Derfor er på DWU1000 Tempdb tilpasset 3,99 TB |


## <a name="database-objects"></a>Databaseobjekter

| Kategori          | Beskrivelse                                  | Maksimum            |
| :---------------- | :------------------------------------------- | :----------------- |
| Database          | Maksimal størrelse                                     | 240 TB komprimeret på disk<br/><br/>Her er uafhængig af tempdb eller log mellemrum, og derfor går meget op i dette område med permanente tabeller.  Grupperet columnstore komprimering er beregnede på 5 X.  Denne komprimering gør det muligt for databasen til vokser til cirka 1 PB, når alle tabeller er grupperet columnstore (standardtypen for tabellen).|
| Tabel             | Maksimal størrelse                                     | 60 TB komprimeret på disk   |
| Tabel             | Tabeller i databasen                          | 2 milliarder          |
| Tabel             | Kolonner i tabellen                            | 1024 kolonner       |
| Tabel             | Byte hver kolonne                             | Afhængigt af kolonnen [datatype][].  Grænsen er 8000 for tegn datatyperne 4000 for nvarchar eller 2 GB for maks datatyper.|
| Tabel             | Byte hver række, definerede størrelse                  | 8060 byte<br/><br/>Antallet af byte hver række er beregnet på samme måde, som det er til SQL Server med siden komprimering. SQL Data Warehouse understøtter række overløb lagerplads som gør det muligt **variabel længde kolonner** , der skal installeres fra række som SQL Server. Når variabel længde rækker publiceres fra række, gemmes kun 24-byte roden i den primære post. Du kan finde yderligere oplysninger finder [række overløb Data overstiger 8 KB][] MSDN-artikel.|
| Tabel             | Partitioner enkelte tabeller                    | 15.000<br/><br/>For høj ydeevne, anbefaler vi minimere antallet af partitioner skal du mens du stadig understøttende virksomhedens behov. Efterhånden som antallet af partitioner vokser, spild for handlingerne Data Definition Language (DDL) og Data Strengmanipulation sprog (DML) vokser og medfører langsommere.|
| Tabel             | Tegn per partition rammen værdi.| 4000 |
| Indeks             | Ikke-grupperede indeks hver tabel.        | 999<br/><br/>Gælder kun rowstore tabeller.|
| Indeks             | Grupperet indeks hver tabel.            | 1<br><br/>Gælder for både rowstore og columnstore tabeller.|
| Indeks             | Indeks vigtige størrelse.                          | 900 byte.<br/><br/>Gælder kun rowstore indeks.<br/><br/>Indeks på varchar kolonner med en maksimal størrelse på mere end 900 byte kan oprettes, hvis de eksisterende data i kolonnerne ikke overstiger 900 byte, når du har oprettet et indeks. Indsæt dog senere, eller Opdater handlinger på de kolonner, der kan medføre den samlede størrelse overskrider 900 byte mislykkes.|
| Indeks             | Vigtige kolonner per indeks.                   | 16<br/><br/>Gælder kun rowstore indeks. Grupperet columnstore indeks medtage alle kolonner.|
| Statistik        | Størrelsen af de kombinerede kolonneværdier.      | 900 byte.         |
| Statistik        | Kolonner per statistik objekt.           | 32                 |
| Statistik        | Statistik, der er oprettet på kolonner i tabellen. | 30.000            |
| Lagrede procedurer | Maksimale antal indlejringsniveauer.               | 8                 |
| Få vist              | Kolonner i visningen                         | 1.024             |


## <a name="loads"></a>Indlæse

| Kategori          | Beskrivelse                                  | Maksimum            |
| :---------------- | :------------------------------------------- | :----------------- |
| Polybase belastning    | Byte hver række                                | 32,768<br/><br/>Polybase indlæse er begrænset til indlæsning rækker begge mindre end 32K og kan ikke indlæses til VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX).  Mens denne grænse findes i dag, fjernes den ret snart.<br/><br/>


## <a name="queries"></a>Forespørgsler

| Kategori          | Beskrivelse                                  | Maksimum            |
| :---------------- | :------------------------------------------- | :----------------- |
| Forespørgsel             | I kø forespørgsler på bruger tabeller.               | 1000               |
| Forespørgsel             | Samtidige forespørgsler på systemvisninger.          | 100                |
| Forespørgsel             | I kø forespørgsler på systemvisninger               | 1000               |
| Forespørgsel             | Maksimale parametre                           | 2098               |
| Batchen             | Maksimal tekststørrelse                                 | 65, 536 * 4096        |
| Vælg resultater    | Kolonner i række                              | 4096<br/><br/>Du kan aldrig have mere end 4096 kolonner hver række i Vælg resultatet. Der er ingen garanti, kan du altid har 4096. Hvis forespørgselsplanen kræver en midlertidig tabel, årsager 1024 kolonner i tabellen maksimale.|
| VÆLG            | Indlejrede underforespørgsler                            | 32<br/><br/>Du kan aldrig have mere end 32 indlejrede underforespørgsler i en SELECT-sætning. Der er ingen garanti, kan du altid har 32. En JOINFORBINDELSE kan for eksempel en en underforespørgsel i forespørgselsplanen. Antallet af underforespørgsler kan også være begrænset af tilgængelig hukommelse.|
| VÆLG            | Kolonner i JOINFORBINDELSE                             | 1024 kolonner<br/><br/>Du kan aldrig have mere end 1.024 kolonner i JOINFORBINDELSEN. Der er ingen garanti, kan du altid har 1024. Hvis den JOINFORBINDELSE plan kræver en midlertidig tabel med flere kolonner end Deltag i resultatet, gælder grænsen på 1024 for den midlertidige tabel. |
| VÆLG            | Byte per GROUP BY kolonner.                  | 8060<br/><br/>Kolonnerne i GROUP BY-delsætningen kan have op til 8060 byte.|
| VÆLG            | Byte per ORDER BY-kolonner                   | 8060 byte.<br/><br/>Kolonnerne i delsætningen ORDER BY kan have op til 8060 byte.|
| Id'er og konstanter per sætning | Antallet af der refereres til id'er og konstanter. | 65.535<br/><br/>SQL Data Warehouse begrænser antallet af id'er og konstanter, der kan være i et enkelt udtryk for en forespørgsel. Denne grænse er 65.535. Overstiger dette nummer resultater i SQL Server-fejl 8632. Du kan finde flere oplysninger, se [Intern fejl: et udtryk services er nået][].|


## <a name="metadata"></a>Metadata

| Systemvisningen                        | Maksimalt antal rækker |
| :--------------------------------- | :------------|
| sys.dm_pdw_component_health_alerts | 10.000       |
| sys.dm_pdw_dms_cores               | 100          |
| sys.dm_pdw_dms_workers             | Samlet antal DMS medarbejdere til den seneste 1000 SQL anmodninger. |
| sys.dm_pdw_errors                  | 10.000       |
| sys.dm_pdw_exec_requests           | 10.000       |
| sys.dm_pdw_exec_sessions           | 10.000       |
| sys.dm_pdw_request_steps           | Samlet antal trin til de seneste 1000 SQL anmodninger, der er gemt i sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs           | 10.000       |
| sys.dm_pdw_sql_requests            | De seneste 1000 SQL anmodninger, der er gemt i sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger, [SQL Data Warehouse Referenceoversigt][].

<!--Image references-->

<!--Article references-->
[Data Warehouse enheder (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[SQL Data Warehouse Referenceoversigt]: ./sql-data-warehouse-overview-reference.md
[Administration af arbejdsbelastning]: ./sql-data-warehouse-develop-concurrency.md
[TempDB]: ./sql-data-warehouse-tables-temporary.md
[datatype]: ./sql-data-warehouse-tables-data-types.md
[oprette en supportbilletter]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Række-overløb Data overstiger 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Intern fejl: et udtryk services er nået]: https://support.microsoft.com/kb/913050
