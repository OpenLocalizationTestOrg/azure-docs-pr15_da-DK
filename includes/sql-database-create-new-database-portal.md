
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>Oprette en ny Azure SQL-database

Brug følgende trin på portalen Azure til at oprette en ny Azure SQL-database på en ny eller eksisterende Azure SQL-Database logiske server.

1. Hvis du er i øjeblikket ikke forbindelse, du opretter forbindelse til [Azure-portalen](http://portal.azure.com).
2. Klik på **Ny**, Skriv **SQL-Database**, og klik derefter på **SQL-Database (ny database)**.

     ![Ny database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Klik på **SQL-Database (ny database)**.

     ![Ny database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)

4. Klik på **Opret** for at oprette en ny database i tjenesten SQL-Database.

     ![Ny database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Levere værdierne til følgende serveregenskaber:

 - Databasenavn
 - Abonnement: Dette gælder kun, hvis du har flere abonnementer.
 - Ressourcegruppe: Hvis du er i opstartsfasen, kan du bruge ressourcegruppe på den logiske server.
 - Vælg datakilde: Du kan vælge en tom database, en sikkerhedskopi af Azure databasen eller eksempeldata. For at overføre en lokal SQL Server-database eller indlæse data ved hjælp af værktøjet BCP kommandolinjen skal du se linkene i slutningen af denne artikel.
 - Server: En ny eller eksisterende logiske server.
 - Server administrator-logon
 - Adgangskode
 - Priser niveau: Hvis du er i opstartsfasen, brug standardværdien S0.
 - Sortering: Dette gælder kun, hvis der er valgt en tom database.

        ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Klik på **Opret**. Du kan se, at installation er startet i meddelelsesområdet.

     ![Ny database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Vent til installation at afslutte før du fortsætter til næste trin.

     ![Ny database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)
