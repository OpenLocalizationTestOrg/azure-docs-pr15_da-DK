<properties
   pageTitle="Komme i gang med SQL-Database dynamiske Data Masking (Azure klassisk Portal)"
   description="Hvordan du kan komme i gang med SQL-Database dynamiske Data Masking i portalen Azure klassisk"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# <a name="get-started-with-sql-database-dynamic-data-masking-azure-classic-portal"></a>Komme i gang med SQL-Database dynamiske Data Masking (Azure klassisk Portal)

> [AZURE.SELECTOR]
- [Dynamiske Data Maskering - Azure-portalen](sql-database-dynamic-data-masking-get-started.md)

## <a name="overview"></a>Oversigt

SQL-Database dynamiske Data Masking begrænser følsomme data visning ved at kombinere til brugere uden rettigheder. Dynamiske data Maskering understøttes for version 12 versionen af Azure SQL-Database.

Dynamiske data Maskering hjælper med at forhindre uautoriseret adgang til følsomme data ved at aktivere kunder til at angive, hvor meget følsomme data for at få vist med minimale virkning på programlag. Det er en sikkerhed baseret på politik funktion, der skjuler følsomme dataene i resultatsættet for en forespørgsel over udpegede databasefelter, mens dataene i databasen ikke ændres.

For eksempel en kundeservicerepræsentant på et callcenter kan identificere ringer af flere cifre i deres CPR-nummer eller kreditkortnummer, men disse dataelementer skal ikke være fuldt synlig i hånden. En regel for Maskering kan defineres, masker alle, men de sidste fire cifre i en hvilken som helst CPR-nummer eller kreditkortnummer i resultatet angivet af en forespørgsel. En relevante data maske kan defineres for at beskytte personlige oplysninger (PII) data, som et andet eksempel, så en udvikler kan forespørge fremstilling miljøer i forbindelse med fejlfinding uden overskridelse overholdelse af regler.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL-Database dynamiske Data Masking grundlæggende

Du kan konfigurere dynamiske data masking politik på portalen Azure klassisk under fanen overvågning og sikkerhed for din database.


> [AZURE.NOTE] For at konfigurere dynamiske data i portalen Azure masking skal du se [Introduktion til SQL Database dynamiske Data Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md).


### <a name="dynamic-data-masking-permissions"></a>Dynamiske data Maskering tilladelser

Dynamiske data Maskering kan konfigureres af administratoren Azure-Database, server-administrator eller sikkerhed ansat roller.

### <a name="dynamic-data-masking-policy"></a>Dynamiske data Maskering politik

* **SQL-brugere, der er udeladt fra Maskering** - et sæt af SQL-brugere eller AAD identiteter, som får umaskeret data i SQL forespørgselsresultaterne. Bemærk, at brugere med administratorrettigheder altid udelades fra Maskering, og får vist de oprindelige data uden en inputmaske.

* **Masking regler** - et sæt regler, der definerer de angivne felter for at blive maskeret og funktionen Maskering, der skal bruges. De angivne felter kan defineres ved hjælp af et skema databasenavnet, tabelnavn og kolonnenavn.

* **Masking funktioner** – en række metoder, der styrer visning af data for forskellige scenarier.

| Masking funktionen | Masking logik |
|----------|---------------|
| **Standard**  |**Fuld Maskering ifølge datatyperne for de angivne felter**<br/><br/>• Brug XXXX eller færre x'er, hvis størrelse i feltet er mindre end 4 tegn for strengdatatyper (nchar, ntext, nvarchar).<br/>• Brug en værdi på nul for numeriske datatyper (bigint bit decimal, heltal, penge, numeriske, smallint, Access, tinyint, flydende, og reelt).<br/>• Brug 01-01-1900 for dato/klokkeslæt-datatyper (dato, datetime2, datetime, datetimeoffset, smalldatetime, tid).<br/>• For SQL variant, standardværdien for den aktuelle type bruges.<br/>• For XML dokumentet <masked/> bruges.<br/>• Brug en tom værdi for speciel datatyper (tidsstempel tabellen, hierarchyid, GUID, binær, billede, varbinary geografiske typer).
| **Kreditkort** |**Masking metode, som viser de sidste fire cifre i de angivne felter** og tilføjer en konstant streng som et præfiks i form af et kreditkort.<br/><br/>XXXX-XXXX-XXXX-1234|
| **CPR-nummer** |**Masking metode, som viser de sidste fire cifre i de angivne felter** og tilføjer en konstant streng som et præfiks i form af en American CPR-nummer.<br/><br/>XXX-XX-1234 |
| **Mail** | **Masking metode, som viser det første bogstav og erstatter domænet med XXX.com** ved hjælp af en konstant streng præfiks i form af en e-mailadresse.<br/><br/>aXX@XXXX.com |
| **Tilfældigt tal** | **Masking metode, som udløser et tilfældigt tal** i overensstemmelse med de valgte grænser og faktisk datatyper. Hvis udpegede grænserne stemmer overens, er funktionen Maskering et konstant tal.<br/><br/>![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Brugerdefineret tekst** | **Masking metode, som viser de første og sidste tegn** og tilføjer en brugerdefineret indre margen streng i midten. Hvis den oprindelige streng er mindre end den udsatte præfiks og et suffiks, bruges den indre margen streng.<br/>Præfiks [indre margen] suffiks<br/><br/>![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-classic-portal"></a>Konfigurere dynamiske data Maskering for databasen ved hjælp af portalen Azure klassisk

1. Start på Azure klassisk portalen på [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Klik på den database, du vil beskære, og klik derefter på fanen **sikkerhed og overvågning** .

3. Under **dynamiske data masking**skal du klikke på **aktiveret** for at aktivere de dynamiske data masking funktion.  

4. Skriv SQL-brugere eller AAD identiteter, der skal udelukkes fra Maskering, og har adgang til de uafmaskede følsomme data. Dette skal være en adskilt af semikolon liste over brugere. Bemærk, at brugere med administratorrettigheder altid har adgang til de oprindelige umaskeret data.

    >[AZURE.TIP] For at gøre det, så programlag kan vise følsomme data til følsomme brugere, skal du tilføje SQL-brugeren eller AAD identitet programmet bruger til at forespørge databasen. Det anbefales, at denne liste indeholder et minimale antal privilegerede brugere at minimere visning af de følsomme data.

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Nederst på siden i menulinjen skal du klikke på **Tilføj MASKE** for at åbne afmaskningen regel konfiguration vindue.

6. Vælg den **skema**, en **tabel** og en **kolonne** på listerne rullemenuen til at definere de angivne felter, der vil blive maskeret.

7. Vælg en **MASKING funktionen** på listen over følsomme data masking kategorier.

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Klik på **OK** i dataene masking regel vindue for at opdatere sæt af masking regler i de dynamiske data masking politik.

9. Klik på **Gem** for at gemme den nye eller opdaterede Maskering politik.


## <a name="set-up-dynamic-data-masking-for-your-database-using-transact-sql-statements"></a>Konfigurere dynamiske data masking for databasen ved hjælp af Transact-SQL-sætninger

Se [dynamiske Data Masking](https://msdn.microsoft.com/library/mt130841.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurere dynamiske data masking for databasen ved hjælp af Powershell-cmdlet'er

Se [Azure SQL Database-cmdletter](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurere dynamiske data Maskering for databasen ved hjælp af REST-API

Få vist [operationer til Azure SQL-databaser](https://msdn.microsoft.com/library/dn505719.aspx).
