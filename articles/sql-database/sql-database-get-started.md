<properties
    pageTitle="SQL-Database Selvstudium: oprette en SQL-database | Microsoft Azure"
    description="Få mere at vide, hvordan du konfigurerer et logisk SQL Database-server, server firewallregel, SQL-database og eksempeldata. Se også, hvordan du kan oprette forbindelse til klientværktøjer, konfigurere brugere og konfigurere en database firewallregel."
    keywords="SQL-database selvstudium, oprette en sql-database"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>SQL-Database Selvstudium: oprette en SQL-database i minutter ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

I dette selvstudium lærer du at bruge Azure-portalen til:

- Oprette en Azure SQL-database med eksempeldata.
- Oprette en serverniveau firewallregel for en enkelt IP-adresse eller et område af IP-adresser.

Du kan udføre de samme opgaver ved hjælp af enten [C#](sql-database-get-started-csharp.md) eller [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Oprette din første Azure SQL-database 

1. Hvis du er i øjeblikket ikke forbindelse, du opretter forbindelse til [Azure-portalen](http://portal.azure.com).
2. Klik på **Ny**, skal du klikke på **Data + lagerplads**, og find derefter **SQL-Database**.

    ![Ny sql database 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Klik på **SQL-Database** for at åbne bladet SQL-Database. Indholdet af denne blade varierer afhængigt af antallet af dine abonnementer og din eksisterende objekter (såsom eksisterende servere).

    ![Ny sql database 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. Angiv et navn for den første database - såsom "Mine-database" i tekstfeltet **Databasenavn** . Et grønt flueben angiver, at du har angivet et gyldigt navn.

    ![Ny sql database 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Hvis du har flere abonnementer, Vælg et abonnement.
6. Klik på **Opret ny** under **ressourcegruppe**, og Angiv et navn til den første ressourcegruppe - såsom "Mine--ressourcegruppe". Et grønt flueben angiver, at du har angivet et gyldigt navn.

    ![Ny sql database 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. Klik på **eksempel** under **Vælg datakilde**, og klik derefter på **AdventureWorksLT [version 12]**under **Vælg eksempel** .

    ![Ny sql database 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. Klik på **Konfigurer påkrævet indstillinger**under **Server**.

    ![Ny sql database 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. Klik på **Opret en ny server**på blade Server. Der oprettes en Azure SQL-database i et serverobjekt,, der kan være enten en ny server eller en eksisterende server.

    ![Ny sql database 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Gennemse bladet **nye server** for at forstå de oplysninger, du har brug for at bruge dem til din nye server.

    ![Ny sql database 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. Angiv et navn til den første server - såsom "Mine-ny-server-objekt" i feltet **servernavn** . Et grønt flueben angiver, at du har angivet et gyldigt navn.

    ![Ny sql database 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. Angiv et brugernavn for administrator-logon til denne server - såsom "Mine--administratorkonto" under **Server administrator logon**. Dette logon er kendt som den primære server-logon. Et grønt flueben angiver, at du har angivet et gyldigt navn.

    ![Ny sql database 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. Under **adgangskode** og **Bekræft adgangskode**, angive en adgangskode til serveren til vigtigste logonkonto - såsom "p@ssw0rd1". Et grønt flueben angiver, at du har angivet en gyldig adgangskode.

    ![Ny sql database 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. Vælg et datacenter, der er relevant at din placering - såsom "Australien Øst" under **placering**.

    ![Ny sql database 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. Under ** oprette version 12 server (seneste opdatering), Bemærk, at du kun har mulighed for at oprette en aktuel version af Azure SQL server.

    ![Ny sql database 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Bemærk, at, som standard i afkrydsningsfeltet **Tillad azure services til at få adgang til server** er markeret og kan ikke ændres her. Dette er en avanceret indstilling. Du kan ændre denne indstilling i firewall serverindstillinger for denne serverobjektet, selvom for de fleste scenarier dette ikke er nødvendigt.

    ![Ny sql database 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. Gennemse dine valg bladet nye server, og klik derefter på **Vælg** for at vælge denne nye server for den nye database.

    ![Ny sql database 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. Klik på **Standard S2** bladet SQL-Database under **priser niveau**, og klik derefter på **grundlæggende** for at vælge det mindst dyr priser niveau for den første database. Du kan altid ændre priser niveau senere.

    ![Ny sql database 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. Gennemse dine valg bladet SQL-Database, og klik derefter på **Opret** for at oprette din første server og database. De værdier, som du angav valideres og installation starter.

    ![Ny sql database 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. Klik på **beskeder** elementer for at kontrollere status for din installation på værktøjslinjen portalen.

    ![Ny sql database 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Når installationen er fuldført, oprettes den nye Azure SQL-server og database i Azure. Du vil ikke kunne oprette forbindelse til din nye server eller database ved hjælp af SQL Server-værktøjer, indtil du opretter en server firewallregel for at åbne firewallen på forbindelser fra uden for Azure SQL-Database.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Næste trin
Nu, hvor du har færdiggjort selvstudiet SQL-Database og oprettet en database med nogle eksempeldata, du er klar til at udforske ved hjælp af dine foretrukne værktøjer.

- Hvis du kender til Transact-SQL og SQL Server Management Studio (SSMS), kan du se Sådan [Connect og forespørgsel en SQL-database med SSMS](sql-database-connect-query-ssms.md).

- Hvis du kender Excel, kan du se hvordan du kan [oprette forbindelse til en SQL-database i Azure med Excel](sql-database-connect-excel.md).

- Hvis du er klar til at starte kodningssprog, Vælg din programmeringssprog på [dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md).

- Hvis du vil flytte dine lokale SQL Server-databaser til Azure, skal du se [overføre en database til SQL-Database](sql-database-cloud-migrate.md) til at få mere at vide.

- Hvis du vil indlæse nogle data i en ny tabel fra en CSV-fil ved hjælp af værktøjet BCP kommandolinjen, skal du se [indlæsning af data i SQL-Database fra en CSV-fil ved hjælp af BCP](sql-database-load-from-csv-with-bcp.md).

- Hvis du vil se dig omkring Azure SQL-Database sikkerhed, skal du se [Introduktion til sikkerhed](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Yderligere ressourcer

[Hvad er SQL-Database?](sql-database-technical-overview.md)
