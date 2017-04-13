### <a name="prerequisites"></a>Forudsætninger
- En Azure konto Du kan oprette en [gratis konto](https://azure.microsoft.com/free)
- En [Azure SQL-Database](../articles/sql-database/sql-database-get-started.md) med dens forbindelsesoplysningerne, herunder servernavn, databasenavn og brugernavn og adgangskode. Disse oplysninger er inkluderet i forbindelsesstrengen SQL-Database:
  
    Server = tcp:*yoursqlservername*. database.windows.net,1433;Initial katalog =*yourqldbname*; Vedvarende sikkerhedsoplysninger = False. Bruger-ID = {your_username}; Adgangskode = {your_password}; MultipleActiveResultSets = False. Kryptere = SAND; TrustServerCertificate = False. Forbindelsen afbrydes efter = 30;

    Læs mere om [Azure SQL-databaser](https://azure.microsoft.com/services/sql-database).

> [AZURE.NOTE] Når du opretter en Azure SQL-Database, kan du også oprette for Eksempeldatabaser, der følger med SQL. 



Før du bruger din Azure SQL-Database i en logik app, oprette forbindelse til din SQL-Database. Du kan gøre dette nemt i din logik app på Azure-portalen.  

Oprette forbindelse til din Azure SQL-Database, der er at benytte følgende fremgangsmåde:  

1. Oprette en logik app. Tilføj en udløser i designvisningen logik Apps, og Tilføj derefter en handling. Vælg **Vis Microsoft administrerede API'er** i på rullelisten, og angiv derefter "sql" i søgefeltet. Vælg en af handlingerne:  

    ![Trin i oprettelsen af SQL Azure-forbindelse](./media/connectors-create-api-sqlazure/sql-actions.png)

2. Hvis du ikke tidligere har oprettet alle forbindelser til SQL-Database, bliver du bedt om forbindelsesoplysningerne:  

    ![Trin i oprettelsen af SQL Azure-forbindelse](./media/connectors-create-api-sqlazure/connection-details.png) 

3. Angiv oplysninger SQL-Database. Egenskaber med en stjerne er påkrævet.

    | Egenskaben | Detaljer |
|---|---|
| Oprette forbindelse via Gateway | Lad dette ikke er markeret. Denne kode bruges ved oprettelse af forbindelse til en lokal SQL Server. |
| Forbindelsesnavn * | Angiv et navn til din forbindelse. | 
| Navnet på SQL Server * | Angiv navnet på serveren; der er noget i retning af *servername.database.windows.net*. Servernavnet er vises i Egenskaber for SQL-Database i portalen Azure og vises også i forbindelsesstrengen. | 
| SQL-databasenavn * | Skriv det navn, du har givet din SQL-Database. Dette er angivet i egenskaberne, SQL-Database i forbindelsesstrengen: Indledende katalog =*yoursqldbname*. | 
| Brugernavn * | Angiv det brugernavn, du har oprettet, da den SQL-Database blev oprettet. Dette er angivet i egenskaberne, SQL-Database i portalen Azure. | 
| Adgangskode * | Skriv den adgangskode, du har oprettet, da den SQL-Database blev oprettet. | 

    Disse legitimationsoplysninger anvendes til at tillade, at din logik app til at oprette forbindelse, og få adgang til dine SQL-data. Når dette er færdigt, ligner din forbindelsesoplysningerne til følgende:  

    ![Trin i oprettelsen af SQL Azure-forbindelse](./media/connectors-create-api-sqlazure/sample-connection.png) 

4. Vælg **Opret**. 

5. Bemærk, at forbindelsen er oprettet. Fortsæt nu med andre trinnene i din app, logik: 

    ![Trin i oprettelsen af SQL Azure-forbindelse](./media/connectors-create-api-sqlazure/table.png)