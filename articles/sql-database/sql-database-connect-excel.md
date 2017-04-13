<properties
    pageTitle="Oprette forbindelse Excel til SQL-Database | Microsoft Azure"
    description="Få mere at vide, hvordan du knytter Microsoft Excel til Azure SQL-database i skyen. Importere data til Excel til rapportering og udforske."
    services="sql-database"
    keywords="oprette forbindelse til sql i excel, importere data til excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>SQL-Database Selvstudium: oprette forbindelse Excel til en Azure SQL-database og oprette en rapport

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Lær, hvordan du forbinder Excel til en SQL-database i skyen, så du kan importere data og oprette tabeller og diagrammer, der er baseret på værdierne i databasen. I dette selvstudium, du vil oprette forbindelse mellem Excel og en databasetabel, gemme den fil, som lagrer data og forbindelsesoplysningerne for Excel og derefter oprette et pivotdiagram fra databaseværdier.

Før du går i gang, skal du bruge en SQL-database i Azure. Hvis du ikke har en, kan du se [oprette din første SQL-database](sql-database-get-started.md) for at få en database med eksempeldata op og kører i et par minutter. Du skal importere eksempeldata til Excel fra denne artikel i denne artikel, men du kan følge lignende trin på dine egne data.

Du skal også en kopi af Excel. I denne artikel bruger [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Oprette forbindelse Excel til en SQL-database og oprette en odc-fil

1.  Åbn Excel for at forbinde Excel til SQL-database, og Opret en ny projektmappe eller åbne en eksisterende Excel-projektmappe.

2.  På menulinjen øverst på siden skal du klikke på **Data**, skal du klikke på **Fra andre kilder**og derefter klikke på **Fra SQL Server**.

    ![Vælg datakilde: forbinde Excel til SQL-database.](./media/sql-database-connect-excel/excel_data_source.png)

    Guiden Dataforbindelse åbnes.

3.  Skriv på SQL-Database **servernavn** , du vil oprette forbindelse til i formularen i dialogboksen **Opret forbindelse til databaseserveren** <*servernavn*>**. database.windows.net**. For eksempel, **adworkserver.database.windows.net**.

4.  Under **legitimationsoplysninger til logon**, skal du klikke på **Brug følgende brugernavn og adgangskode**, og skriv det **Brugernavn** og **adgangskode** du konfigurere til SQL-databaseserver, da du oprettede den, og klik derefter på **Næste**.

    ![Skriv navn, og log serveroplysninger](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] Du kan muligvis ikke oprette forbindelse afhængigt af dit netværksmiljø, eller du kan miste forbindelsen, hvis SQL-databaseserveren ikke tillader trafik fra din klient IP-adresse. Gå til [Azure-portalen](https://portal.azure.com/), klik på SQL-servere, skal du klikke på din server, skal du klikke på firewall under indstillinger og tilføje din klient IP-adresse. Du kan finde oplysninger i [at konfigurere firewall-indstillinger](sql-database-configure-firewall-settings.md) .

5. Vælg den database, du vil arbejde med på listen i dialogboksen **Vælg Database og tabel** , og klik derefter på de tabeller eller visninger, du vil arbejde med (Vi valgte **vGetAllCategories**), og klik derefter på **Næste**.

    ![Vælg en database og tabel.](./media/sql-database-connect-excel/select-database-and-table.png)

    Dialogboksen **Gem dataforbindelsesfil, og Afslut** åbnes, hvor du kan få oplysninger om filen Office database connection (*.odc), som Excel bruger. Du kan beholde standardindstillingerne eller tilpasse dine valg.

6. Du kan lade standardindstillingerne, men du kan notere **Filnavn** især. En **Beskrivelse**, et **Fuldt navn**og en **Nøgleord til søgning** hjælpe dig og andre brugere huske, hvad du opretter forbindelse til, og find forbindelsen. Klik på **altid forsøger at bruge denne fil for at opdatere data** , hvis du vil forbindelsesoplysninger, der er gemt i odc-filen, så den kan opdatere, når du opretter forbindelse til den, og klik derefter på **Udfør**.

    ![Gemme en odc-fil](./media/sql-database-connect-excel/save-odc-file.png)

    Dialogboksen **importdata** vises.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importere data til Excel og oprette et pivotdiagram
Nu, hvor du har oprettet forbindelsen og oprettet filen med data og forbindelse, du læser for at importere dataene.

1. Klik på den indstilling, du vil bruge til at præsentere dine data i regnearket i dialogboksen **Importdata** , og klik derefter på **OK**. Vi valgte **et pivotdiagram**. Du kan også vælge at oprette et **nyt regneark** eller **Føj disse data til en datamodel**. Du kan finde flere oplysninger om datamodeller, [oprette en datamodel i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Klik på **Egenskaber** til at udforske oplysninger om odc-fil, du oprettede i forrige trin og vælge indstillinger for opdatering af data.

    ![Vælge format for data i Excel](./media/sql-database-connect-excel/import-data.png)

    I regnearket er nu en tom pivottabel og diagram.

8. Vælg alle-afkrydsningsfelterne for de felter, du vil have vist under **Pivottabelfelter**.

    ![Konfigurere databaserapport.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Hvis du vil forbinde andre Excel-projektmapper og regneark til databasen, klik på **Data**, skal du klikke på **forbindelser**, klik på **Tilføj**, Vælg den forbindelse, du har oprettet på listen og klik derefter på **Åbn**.
> ![Åbne en forbindelse fra en anden projektmappe](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [oprette forbindelse til SQL-Database med SQL Server Management Studio](sql-database-connect-query-ssms.md) til avancerede forespørgsler og analyse.
- Få mere at vide om fordelene ved [elastiske grupper](sql-database-elastic-pool.md).
- Lær, hvordan du [opretter et webprogram, der opretter forbindelse til SQL-Database på back end-](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
