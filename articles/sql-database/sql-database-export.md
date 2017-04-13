<properties
    pageTitle="Arkivere en Azure SQL-database til en BACPAC fil ved hjælp af portalen Azure"
    description="Arkivere en Azure SQL-database til en BACPAC fil ved hjælp af portalen Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Arkivere en Azure SQL-database til en BACPAC fil ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Denne artikel indeholder anvisninger til at arkivere dine Azure SQL-database til en BACPAC-fil (gemt i Azure blob-lager) ved hjælp af [Azure-portalen](https://portal.azure.com).

Når du har brug at oprette et arkiv med en Azure SQL-database, kan du eksportere databaseskema og data til en BACPAC fil. En BACPAC fil er blot en ZIP-fil med filtypenavnet BACPAC. En BACPAC fil kan senere gemmes, i Azure blob-lager eller lokale lager i en lokal placering og senere importerede tilbage til Azure SQL-Database eller til en SQL Server lokalt installation. 

***Overvejelser i forbindelse med***

- For et arkiv skal være en transaktion ensartet, skal du sikre dig enten, ingen Skriv aktivitet bliver til virkelighed under eksporten eller, der skal eksporteres fra en [en transaktion ensartet kopi](sql-database-copy.md) af din Azure SQL-database.
- Den maksimale størrelse på en BACPAC fil arkiveret til Azure Blob-lager er 200 GB. Hvis du vil arkivere en større BACPAC-fil til det lokale lager, du brug værktøjet [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandoprompt. Dette værktøj leveres med både Visual Studio og SQL Server. Du kan også [hente](https://msdn.microsoft.com/library/mt204009.aspx) den nyeste version af SQL Server Data Tools til at få dette værktøj.
- Arkivering til Azure premium lager ved hjælp af en BACPAC fil understøttes ikke.
- Hvis eksporten overskrider 20 timers, kan den annulleres. For at øge ydeevnen under eksport, kan du:
 - Midlertidigt øge serviceniveau.
 - Ophøre alle læse og skrive aktivitet under eksporten.
 - Bruge et [grupperet indeks](https://msdn.microsoft.com/library/ms190457.aspx) med ikke-null-værdier på alle store tabeller. En eksport mislykkes uden grupperet indeks, hvis det tager længere tid end 6-12 timer. Dette skyldes, at tjenesten Eksportér skal udføre en tabelscanning for at forsøge at eksportere hele tabellen. Der er en god metode til at finde ud af, hvis dine tabeller er optimeret til eksport til at køre **DBCC SHOW_STATISTICS** , og Sørg for, at *RANGE_HI_KEY* ikke er null og dens værdi har god fordeling. Yderligere oplysninger finder du [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] BACPACs er ikke beregnet til at bruges til sikkerhedskopiering og gendannelse handlinger. Azure SQL-Database opretter automatisk sikkerhedskopiering for hver brugerdatabase. Yderligere oplysninger finder du [Business løbende oversigt](sql-database-business-continuity.md).

Du skal bruge følgende for at fuldføre i denne artikel:

- Et Azure-abonnement.
- Azure SQL-Database. 
- En [Azure Standard lager konto](../storage/storage-create-storage-account.md) hos en blob objektbeholder til at gemme BACPAC i standard-lager.

## <a name="export-your-database"></a>Eksportere din database

Åbn bladet SQL-Database til den database, du vil eksportere.

> [AZURE.IMPORTANT] For at sikre en en transaktion ensartet BACPAC fil skal du første [oprette en kopi af databasen](sql-database-copy.md) og derefter eksportere kopien af databasen. 

1.  Gå til [Azure-portalen](https://portal.azure.com).
2.  Klik på **SQL-databaser**.
3.  Klik på databasen at arkivere.
4.  Klik på **Eksporter** for at åbne bladet **eksportere database** i bladet SQL-Database:

    ![knappen eksport][1]

5.  Klik på **lager** , og vælg din konto og blob objektbeholder til lagring hvor BACPAC skal gemmes:

    ![eksportere database][2]

6. Vælg din godkendelsestype. 
7.  Angiv de relevante legitimationsoplysninger for den Azure SQL-server, der indeholder den database, du vil eksportere.
8.  Klik på **OK** for at arkivere databasen. Klik på **OK** opretter en anmodning om en Eksportér database og sender den til tjenesten. Hvor lang tid tager Eksportér afhænger af størrelsen og kompleksiteten af databasen, og din tjeneste niveau. Du modtager en besked.

    ![eksportere meddelelse][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Overvåge forløbet af eksport

1.  Klik på **SQL-servere**.
2.  Klik på den server, der indeholder den oprindelige (kilde)-database, du lige har arkiveret.
3.  Rul ned til handlinger.
4.  Klik på **Importér/Eksportér oversigt**i bladet SQL server:

    ![importere Eksportér oversigt][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Bekræft BACPAC er i din objektbeholder til lagring

1.  Klik på **lagerplads konti**.
2.  Klik på kontoen lagerplads, hvor du har gemt BACPAC arkiv.
3.  Klik på **beholdere** , og vælg objektbeholderen du eksporterede databasen til få mere at vide (du kan hente og gemme BACPAC her).

    ![oplysninger om .bacpac][5]  

## <a name="next-steps"></a>Næste trin

- For at få mere for at vide om at importere en BACPAC til en Azure SQL-Database, skal du se [importere en BACPCAC til en Azure SQL-database](sql-database-import.md)
- Hvis du vil have for at vide om at importere en BACPAC til en SQL Server-database skal du se [importere en BACPCAC til en SQL Server-database](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

