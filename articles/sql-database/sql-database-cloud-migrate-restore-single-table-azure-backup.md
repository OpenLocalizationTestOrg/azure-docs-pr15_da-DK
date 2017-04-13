<properties
    pageTitle="Gendanne en enkelt tabel fra Azure SQL-Database sikkerhedskopi | Microsoft Azure"
    description="Lær, hvordan du kan gendanne en enkelt tabel fra Azure SQL-Database sikkerhedskopi."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Sådan gendannes en enkelt tabel fra en sikkerhedskopi af Azure SQL-Database

Du kan støde på en situation, hvor du ved et uheld ændret nogle data i en SQL-database og nu vil gendanne den pågældende enkelte tabel. I denne artikel beskrives, hvordan du gendanner en enkelt tabel i en database fra et af [automatisk sikkerhedskopiering](sql-database-automated-backups.md)af SQL-Database.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Forberedelse: omdøbe tabellen og gendanne en kopi af databasen
1. Identificere tabellen i din Azure SQL-database, du vil erstatte med den gendannede kopi. Bruge Microsoft SQL Management Studio til at omdøbe tabellen. For eksempel Omdøb tabellen som &lt;tabelnavn&gt;_GL.

    **Bemærk!** Hvis du vil undgå at blive blokeret, Sørg for, at der ikke er nogen aktivitet, der kører på den tabel, du er ved at omdøbe. Hvis du støder på problemer, skal du kontrollere, som udfører denne procedure under et vedligeholdelsesvindue.

2. Gendanne en sikkerhedskopi af databasen til et punkt i gang, du vil gendanne til ved hjælp af [Punkt In_Time gendanne](sql-database-recovery-using-backups.md#point-in-time-restore) trin.

    **Noter**:
    - Navnet på den gendannede database bliver i DBName + tidsstempel format. for eksempel, **Adventureworks2012_2016-01-01T22-12Z**. Dette trin overskrive ikke eksisterende databasenavnet på serveren. Dette er en måling af sikkerhed, og det er beregnet til at gør det muligt at kontrollere den gendannede database, inden de slippe deres aktuelle database, og Omdøb den gendannede database til daglig drift.
    - Alle ydeevne dele fra grundlæggende til Premium sikkerhedskopieres automatisk af tjenesten, med varierende sikkerhedskopiering opbevaring statistik, afhængigt af niveauet:

| DB Gendan | Grundlæggende trin | Standard niveauer | Premium niveauer |
| :-- | :-- | :-- | :-- |
|  Gendanne tidspunkt |  Gendanne et punkt i 7 dage|Gendanne et punkt i 35 dage| Gendanne et punkt i 35 dage|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Kopiere tabel fra den gendannede database ved hjælp af overførselsværktøjet SQL-Database
1. Hent og Installer [Guiden Overførsel af SQL-Database](https://sqlazuremw.codeplex.com).

2. Åbn guiden SQL Database migrering, på siden **Vælg proces** , Vælg **Database under analysér/overføre**, og klik derefter på **Næste**.
![SQL-Database overførsel guiden – Vælg proces](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Anvende følgende indstillinger i dialogboksen **Opret forbindelse til Server** :
 - **Servernavn**: din SQL Azure forekomst
 - **Godkendelse**: **SQL Server-godkendelse**. Angiv dine logonoplysninger.
 - **Database**: **Master DB (liste med alle databaser)**.
 - **Bemærk!** Som standard gemmes guiden dine logonoplysninger. Hvis du ikke vil have den, kan du vælge **Glemmer logonoplysninger**.
![SQL-Database overførsel guiden - Vælg kilde - trin 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Vælg gendannede databasenavnet i afsnittet **forberedelse** som din kilde i dialogboksen **Vælg datakilde** , og klik derefter på **Næste**.

    ![SQL-Database overførsel guiden - Vælg kilde - trin 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Vælg indstillingen **Vælg bestemte databaseobjekter** i dialogboksen **Vælg objekter** , og vælg derefter den table(or tables), du vil overføre til den ønskede server.
![SQL-Database overførsel guiden – Vælg objekter](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Klik på **Ja** på siden **Script guiden oversigt** , når du bliver spurgt om hvorvidt du er klar til at oprette en SQL-script. Du har også mulighed for at gemme TSQL scriptet til senere brug.
![SQL-Database overførsel guiden - Script guiden oversigt](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Klik på **Næste**på siden **Oversigt over søgeresultater** .
![SQL-Database overførsel guiden – oversigt over søgeresultater](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Klik på **Opret forbindelse til serveren**på siden **Konfiguration af Target serverforbindelse** , og derefter angive oplysninger på følgende måde:
    - **Servernavn**: Target server-forekomst
    - **Godkendelse**: **SQL Server-godkendelse**. Angiv dine logonoplysninger.
    - **Database**: **Master DB (liste med alle databaser)**. Denne indstilling viser en liste over alle databaser på serveren.

    ![SQL-Database overførsel guiden - Konfiguration Target serverforbindelse](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Klik på **Opret forbindelse**, Vælg den destinationsadresse-database, du vil flytte tabellen for at, og klik derefter på **Næste**. Dette bør har kørt den tidligere oprettet script, og du bør se tabellen nyligt flyttede kopieret til destinationsdatabasen.

## <a name="verification-step"></a>Bekræftelse trin
1. En forespørgsel og teste nyligt kopierede tabellen for at sikre, at dataene er intakt. Når bekræftelsen, kan du slipper tabelformularen omdøbte **forberedelse** sektion. (for eksempel &lt;tabelnavn&gt;_GL).

## <a name="next-steps"></a>Næste trin

[Automatisk sikkerhedskopiering i SQL-Database](sql-database-automated-backups.md)
