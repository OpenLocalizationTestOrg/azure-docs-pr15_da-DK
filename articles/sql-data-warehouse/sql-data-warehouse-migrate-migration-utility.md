<properties
   pageTitle="Overføre: Data lager Migration Utility | Microsoft Azure"
   description="Overføre til SQL datawarehouse."
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
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Data Warehouse Migration Utility (Preview)

> [AZURE.SELECTOR]
- [Hente Migration Utility][]

Værktøjet Data Warehouse overførsel er et værktøj, der er udviklet til at overføre skemaer og data fra SQL Server og Azure SQL-Database til Azure SQL Data Warehouse. Under overflytningen skema knytter værktøjet automatisk det tilsvarende skema fra kilde til destination. Når skemaet er blevet overført, skal værktøjerne, der giver mulighed for at flytte data med automatisk oprettede scripts.

Ud over skemaer og data overførsel giver dette værktøj dig mulighed for at generere kompatibilitetsrapporter, som opsummerer inkompatibilitet mellem mål og kilde forekomster som kunne forhindre strømlinet overførsel.

## <a name="get-started"></a>Komme i gang
Som en forudsætninger for installation skal du værktøjet BCP kommandolinjen til at køre scripts til overførsel og Office til at få vist Kompatibilitetsrapporten. Efter at åbne den eksekverbare fil, der hentes bliver du bedt om at acceptere en standard slutbrugerlicensaftale, før værktøjet skal installeres.

Hvis du vil køre overførsel Utiliy, skal du bruge den følge tilladelser på den database, du vil overføre: oprette DATABASE, JUSTER alle DATABASE eller alle VISNINGSDEFINITION.

### <a name="launching-the-tool-and-connecting"></a>Starter værktøjet og oprette forbindelse
Start værktøjet ved at klikke på ikonet skrivebord, som vises indlæg Installer. Når du åbner værktøjet, bliver du bedt om med en forbindelse første gang-side, hvor du kan vælge din kilde og destination for overførselsværktøjet. På nuværende tidspunkt understøtter vi SQL Server og Azure SQL-Database som kilder og SQL Data Warehouse som destination. Når du har valgt det vil du bedt om at oprette forbindelse til din kildeserveren ved at udfylde i servernavn og godkendelse og derefter klikke på 'Opret forbindelse'.

Efter godkendelse, viser værktøjet en liste over databaser, der findes i den server, som du har forbindelse til. Du kan starte overførslen ved at vælge en database, du vil overføre og derefter klikke på 'Overføre markeret'.

## <a name="migration-report"></a>Overflytningsrapport
Vælge 'Kør Database kompatibilitetskontrol' i værktøjet genererer en rapport, der viser alle objekt inkompatibilitet i databasen, du bedt om at overføre. En mere omfattende liste over nogle af de SQL Server-funktioner, ikke der findes i SQL Data Warehouse kan findes i vores [dokumentation for overførsel][]. Når rapporten er oprettet vil du kunne gemme og åbne rapporten i Excel.

Vær opmærksom på, når der genereres overførsel skemaet, de fleste problemer, der er identificeret som 'Object' justeres for at tillade øjeblikkelig overførsel af de pågældende data. Gennemgå ændringerne for at sikre, at du ikke vil foretage ekstra justeringer før du anvender i skemaet.

## <a name="migrate-schema"></a>Overføre skema

Når du har forbindelse genererer vælge 'Overføre skema' et skema overførsel script for de valgte tabeller. Denne script porte strukturen i tabellen, kort inkompatibel data skriver til mere kompatibel formularer og opretter sikkerhedslegitimationsoplysninger og -Skemafiler, hvis dette er angivet af brugeren i indstillingerne for overførsel. Denne kode kan køres mod forekomsten målrettede SQL Data Warehouse, gemt i en fil, kopieret til Udklipsholder eller redigeres endda integrerede før du udfører yderligere handling.  

Som relevante ovenfor, når overføre skema Gennemse overførslen ændringer, som værktøjet, der har skrevet for at sikre, at, at du fuldt forstår dem.  

## <a name="migrate-data"></a>Overføre data

Du kan generere BCP scripts, som vil flytte dine data først til flade filer på din server, ved at klikke på indstillingen 'Overføre Data' og derefter direkte i dine SQL Data Warehouse. Vi anbefaler denne proces til at flytte lidt af data, og som forsøg ikke er indbygget og fejl kan opstå, hvis der er et tab af netværksforbindelsen. For at køre dette, skal du have værktøjet BCP kommandolinjen installeret og skemaet for dataene, skal allerede har oprettet.

Når du har udfyldt af parametrene ovenfor skal du blot klikke på Kør overførsel, og der skal oprettes et sæt af to pakker til den angivne placering. Køre eksportfilen for at eksportere data fra din migrering kilde i flade filer, og kør den importerede fil for at importere dine data til SQL Data Warehouse.

## <a name="next-steps"></a>Næste trin
Nu, hvor du har overført nogle data, skal du se, hvordan du [udvikler][].

<!--Image references-->

<!--Article references-->
[overførsel dokumentation]: sql-data-warehouse-overview-migrate.md
[udvikle]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Hente Migration Utility]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
