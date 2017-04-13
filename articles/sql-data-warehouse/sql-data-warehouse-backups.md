<properties
   pageTitle="SQL Data Warehouse sikkerhedskopier | Microsoft Azure"
   description="Få mere at vide om SQL Data Warehouse indbyggede databasesikkerhedskopier, der gør det muligt at gendanne en Azure SQL Data Warehouse til et gendannelsespunkt eller et andet geografisk område."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>SQL Data Warehouse sikkerhedskopier

SQL Data Warehouse indeholder både lokale og geografiske sikkerhedskopier som en del af dens data warehouse ekstra funktioner. Disse omfatter Azure lagerplads Blob snapshots og geografisk overflødige lagerplads. Bruge data warehouse sikkerhedskopier til at gendanne dit datawarehouse til et gendannelsespunkt i den primære region eller gendanne til et andet geografisk område. Denne artikel forklares de specifikke oplysninger i sikkerhedskopier i SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>Hvad er en sikkerhedskopiering af data warehouse?

En sikkerhedskopiering af data warehouse er de data, du kan bruge til at gendanne et datawarehouse til et bestemt tidspunkt.  Da SQL Data Warehouse er et distribueret system, består en sikkerhedskopiering af data warehouse af mange filer, der er gemt i Azure BLOB. 

Sikkerhedskopier af databasen er en vigtig del af en hvilken som helst business løbende og genoprettelse efter genoprettelsesstrategi, fordi de beskytte dine data mod utilsigtet beskadigelse eller sletning. Se [Oversigt over Business løbende](../sql-database/sql-database-business-continuity.md)kan finde flere oplysninger.

## <a name="data-redundancy"></a>Overflødige data

SQL Data Warehouse beskytter dine data ved at gemme dine data i lokalt overflødige (LRS) Azure Premium-lager. Denne funktion til Azure-lager gemmer flere synkron kopier af dataene i den lokale datacenter til at garantere transparent databeskyttelse, hvis der er lokaliseret mislykkede forsøg. Overflødige data sikrer, at dine data kan klare infrastruktur problemer som mislykkede forsøg på disken. Overflødige data sikrer Forretningskontinuitet med en fejlsikrede og meget tilgængelige infrastruktur.

Til at få mere at vide om:

- Azure Premium lagring, se [Introduktion til Azure Premium-lager](../storage/storage-premium-storage.md).
- Lokalt overflødige lagring, se [Azure-lager gentagelse](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Azure Blob Storage øjebliksbilleder

Som en fordel ved at bruge Azure Premium lager, bruger SQL Data Warehouse Azure lagerplads Blob snapshots til at sikkerhedskopiere datawarehouse lokalt. Du kan gendanne et datawarehouse til et øjebliksbillede gendannelsespunkt. Snapshots start et minimum af hver otte timer og er tilgængelige for syv dage.  

Til at få mere at vide om:

- Azure blob snapshots, se [oprette et snapshot af blob](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Geografisk overflødige sikkerhedskopier

Døgnet gemmer SQL Data Warehouse fuld datawarehouse i Standard-lager. Fuld datawarehouse oprettes så det svarer til tidspunktet, hvor det sidste øjebliksbillede. Standard opbevaring hører til en geografisk overflødige lagerplads konto med læseadgang (RA-GRS). Funktionen Azure lagerplads RA-GRS kopieres sikkerhedskopifilerne til en [parvis datacenter](../best-practices-availability-paired-regions.md). Denne geografisk-replikering sikrer, at du kan gendanne datalager, i tilfælde af, at du ikke kan få adgang til snapshots i din primære område. 

Denne funktion er aktiveret som standard. Hvis du ikke vil bruge geografisk overflødige sikkerhedskopier, kan du vælge. 

>[AZURE.NOTE] Azure-lager refererer ordet *Gentagelse* til at kopiere filer fra én placering til en anden. SQLS *databasereplikering* refererer til at holde flere sekundære databaser, der er synkroniseret med en primær database. 

Til at få mere at vide om:
- Geografisk overflødige lagring, se [Azure-lager gentagelse](../storage/storage-redundancy.md).
- RA-GRS lagring, se [læseadgang geografisk overflødige lagerplads](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Planen for data warehouse sikkerhedskopiering og opbevaringsperiode

SQL Data Warehouse opretter øjebliksbilleder i dine online data lagre hver fire til otte timer og bevarer hver øjebliksbillede for syv dage. Du kan gendanne databasen online til en af gendannelsespunkter i de seneste syv dage. 

For at se, når det sidste øjebliksbillede i gang skal du køre denne forespørgsel på din online SQL Data Warehouse. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Hvis du vil bevare et øjebliksbillede i mere end syv dage, kan du gendanne et gendannelsespunkt til et nyt datawarehouse. Når gendannelsen er fuldført, starter SQL Data Warehouse oprettelse af snapshots på et nyt datalager. Hvis du ikke ændrer nye datawarehouse, skal være tomt, snapshots og snapshot omkostninger er derfor minimale. Du kan også stoppe databasen for at undgå, at SQL Data Warehouse oprettelse af snapshots.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Hvad sker der med mine sikkerhedskopiering opbevaring, mens mine datawarehouse er afbrudt midlertidigt?

SQL Data Warehouse opretter ikke snapshots og udløber ikke snapshots, mens et datawarehouse er afbrudt midlertidigt. Øjebliksbillede alder ændres ikke, mens datawarehouse er afbrudt midlertidigt. Øjebliksbillede opbevaring er baseret på antallet dage, der er datawarehouse er online, ikke kalenderdage.

Hvis et øjebliksbillede starter oktober 1 4 pm og datawarehouse er afbrudt midlertidigt oktober 3 kl, er snapshot for eksempel to dage gamle. Når datawarehouse kommer tilbage til onlinetilstand er snapshot to dage gammel. Hvis datawarehouse er online oktober 5 kl snapshot er to dage gammel og forbliver i fem flere dage.

Når datawarehouse kommer igen er online, SQL Data Warehouse genoptages nye snapshots og udløber snapshots, når de har mere end syv dage data.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Hvor længe er opbevaringsperiode for et mistede datawarehouse?
Når et datawarehouse afbrydes, gemmes i syv dage og derefter fjernet datawarehouse og snapshots. Du kan gendanne datawarehouse til en af de gemte gendannelsespunkter.

> [AZURE.IMPORTANT] Hvis du sletter en logisk SQL server-forekomst, slettes også alle databaser, der hører til forekomsten, og du kan ikke gendannes. Du kan ikke gendanne en slettet server.

## <a name="data-warehouse-backup-costs"></a>Data warehouse sikkerhedskopiering omkostninger

De samlede omkostninger for din primære datawarehouse og syv dage i Azure Blob snapshots afrundes til nærmeste TB. Hvis dine datawarehouse er 1,5 TB, og snapshots bruger 100 GB, kan du for eksempel er faktureret til 2 TB data til Azure Premium lager rente. 

>[AZURE.NOTE] Hver øjebliksbillede er tom først og vokser, når du foretager ændringer i den primære datalager. Alle snapshots stiger i størrelse, som data warehouse ændringer. Derfor vokse lagerplads omkostningerne for snapshots ifølge hastigheden af ændringer.

Hvis du bruger geografisk overflødige lagerplads, modtager du en separat lagerplads gratis. Geografisk overflødige opbevaring er faktureret til standardsats læseadgang geografisk overflødige lagerplads (RA-GRS).

Se [SQL Data Warehouse priser](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)kan finde flere oplysninger om SQL Data Warehouse priser.

## <a name="using-database-backups"></a>Ved hjælp af databasesikkerhedskopier

Den primære brug efter SQL data warehouse sikkerhedskopier er at gendanne datawarehouse til et punkt Gendan inden for opbevaringsperioden.  

- Hvis du vil gendanne et SQL datawarehouse, skal du se [gendanne et SQL datawarehouse](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Relaterede emner

### <a name="scenarios"></a>Scenarier

- En oversigt til løbende, under [Oversigt over Business løbende](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Hvis du vil gendanne et datalager, skal du se [gendanne et SQL datawarehouse](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

