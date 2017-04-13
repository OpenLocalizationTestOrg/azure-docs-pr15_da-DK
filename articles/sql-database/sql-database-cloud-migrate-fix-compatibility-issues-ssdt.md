<properties
   pageTitle="Løse SQL Server-database kompatibilitetsproblemer før overførsel til SQL-Database | Microsoft Azure"
   description="Microsoft Azure SQL Database, overflytning af databasen, kompatibilitet, SQL Azure overførsel guiden, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Overføre en SQL Server-Database til Azure SQL-Database ved hjælp af SQL Server Data Tools til Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Upgrade Advisor](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

I denne artikel kan lære du at finde og rette SQL Server-database kompatibilitetsproblemer med SQL Server Data Tools til Visual Studio før overførsel til Azure SQL-Database.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Brug af SQL Server Data Tools til Visual Studio

Brug SQL Server Data Tools til Visual Studio ("SSDT") til at importere databaseskemaet til en database i Visual Studio-projekt til analyse. Hvis du vil analysere, skal du angive den destinationsadresse platform til projektet som version 12 SQL-Database og derefter bygge projektet. Hvis Opret er gået igennem, er databasen kompatibel. Hvis Opret mislykkes, kan du løse fejl i SSDT (eller en af de andre værktøjer, der er beskrevet i dette emne). Når projektet opbygger korrekt, kan du publicere det igen som en kopi i kildedatabasen. Du kan derefter bruge funktionen data Sammenlign i SSDT for at kopiere dataene fra kildedatabasen til kompatible version 12 SQL Azure-databasen. Derefter kan du overføre denne opdaterede database. Hvis du vil bruge denne indstilling, skal du hente den [nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![VSSSDT overførsel diagram](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Hvis kun skema overførsel er påkrævet, kan skemaet udgives direkte fra Visual Studio direkte til Azure SQL-Database. Brug denne metode, når databaseskemaet kræver flere ændringer end kan løses ved guiden Overflytning alene.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Registrering af kompatibilitetsproblemer med SQL Server Data Tools til Visual Studio
   
1.  Åbn **SQL Server Object Explorer** i Visual Studio. Bruge **Tilføj SQL Server** til at oprette forbindelse til den SQL Server-forekomst, der indeholder den database, der overføres. Find databasen i Object Explorer, højreklik på databasen, og vælg **Opret nyt projekt...**     
    
    ![Nyt projekt](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Konfigurere importindstillingerne importere **program-fastsat objekter kun**. Fjern markeringen i indstillingerne for import af følgende: refereres til logon, tilladelser og databaseindstillinger.    

    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Klik på **Start** for at importere databasen, og oprette projektet, der indeholder en T-SQL-scriptfil for hvert objekt i databasen. Script-filerne er indlejret i mapper i projektet.    

    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  Højreklik på database projektet i Visual Studio Solution Explorer, og vælg Egenskaber. Konfigurere Target platformen til Microsoft Azure SQL-Database version 12 på siden **Indstillinger for Project** .    
    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Højreklik på projektet, og vælg **opbygge** til at oprette projektet.    
    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  På **Listen over fejl** vises hver inkompatibilitet. I dette tilfælde er brugernavnet NT AUTHORITY\NETWORK SERVICE ikke kompatibel. Eftersom den er kompatibel, kan du skrive en kommentar ud af det eller fjerne den (og adresse konsekvenserne af fjerne denne logon og rolle fra løsningen database).     
    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Løse kompatibilitetsproblemer med SQL Server Data Tools til Visual Studio

1.  Dobbeltklik på det første script for at åbne scriptet i et forespørgselsvindue og kommentar ud scriptet, og udfør derefter scriptet.     
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Gentag denne proces for hver script, der indeholder inkompatibilitet, indtil der ingen fejl tilbage.    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Når databasen er fri for fejl, skal du højreklikke på projektet, og vælg **Publicer**. En kopi af kildedatabasen er bygget og publiceret (det anbefales at bruge en kopi i det mindste først).     
 - Før du publicerer, afhængigt af SQL Server kildeversionen (tidligere end SQL Server 2014), du muligvis nulstille projektets mål platform for at aktivere installation.     
 - Hvis du overfører en ældre SQL Server-database, ikke indført alle elementer i det projekt, der ikke understøttes i kilden SQL Server indtil overføre databasen til en nyere version af SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  Højreklik på kildedatabasen SQL Server Object Explorer, og klik på **Sammenligning af Data**. Sammenligning af projektet til den oprindelige database hjælper dig med at forstå, hvilke ændringer der er foretaget af guiden. Vælg din version 12 Azure SQL-version af databasen, og klik derefter på **Udfør**.    
    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Gennemgå forskellene registreres, og klik derefter på **Opdater destination** for at overføre data fra kildedatabasen til version 12 SQL Azure-databasen.     
    
    ![alternativ tekst](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Vælg en installationsmetode. Se [overflytte en kompatible SQL Server-database til SQL-Database.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
