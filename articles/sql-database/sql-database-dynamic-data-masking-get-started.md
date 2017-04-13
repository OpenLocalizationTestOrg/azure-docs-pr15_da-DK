<properties
   pageTitle="Komme i gang med SQL-Database dynamiske Data Masking (Azure Portal)"
   description="Hvordan du kan komme i gang med SQL-Database dynamiske Data Masking i portalen Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>Komme i gang med SQL-Database dynamiske Data Masking (Azure Portal)

> [AZURE.SELECTOR]
- [Dynamiske Data Maskering - Azure klassisk Portal](sql-database-dynamic-data-masking-get-started-portal.md)

## <a name="overview"></a>Oversigt

SQL-Database dynamiske Data Masking begrænser følsomme data visning ved at kombinere til brugere uden rettigheder. Dynamiske data Maskering understøttes for version 12 versionen af Azure SQL-Database.

Dynamiske data Maskering hjælper med at forhindre uautoriseret adgang til følsomme data ved at aktivere kunder til at angive, hvor meget følsomme data for at få vist med minimale virkning på programlag. Det er en sikkerhed baseret på politik funktion, der skjuler følsomme dataene i resultatsættet for en forespørgsel over udpegede databasefelter, mens dataene i databasen ikke ændres.

For eksempel en kundeservicerepræsentant på et callcenter kan identificere ringer af flere cifre i deres CPR-nummer eller kreditkortnummer, men disse dataelementer skal ikke være fuldt synlig i hånden. En regel for Maskering kan defineres, masker alle, men de sidste fire cifre i en hvilken som helst CPR-nummer eller kreditkortnummer i resultatet angivet af en forespørgsel. En relevante data maske kan defineres for at beskytte personlige oplysninger (PII) data, som et andet eksempel, så en udvikler kan forespørge fremstilling miljøer i forbindelse med fejlfinding uden overskridelse overholdelse af regler.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL-Database dynamiske Data Masking grundlæggende

Du kan konfigurere en dynamiske data masking politik på portalen Azure ved at vælge handlingen dynamiske Data Masking i SQL-Database konfiguration blade eller indstillinger blade.


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
| **Tilfældigt tal** | **Masking metode, som udløser et tilfældigt tal** i overensstemmelse med de valgte grænser og faktisk datatyper. Hvis udpegede grænserne stemmer overens, er funktionen Maskering et konstant tal.<br/><br/>![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Brugerdefineret tekst** | **Masking metode, som viser de første og sidste tegn** og tilføjer en brugerdefineret indre margen streng i midten. Hvis den oprindelige streng er mindre end den udsatte præfiks og et suffiks, bruges den indre margen streng. <br/>Præfiks [indre margen] suffiks<br/><br/>![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### <a name="recommended-fields-to-mask"></a>Anbefalede felter til maske

DDM anbefalinger program markerer bestemte felter fra databasen som følsomme felter, som kan være gode kandidater til Maskering. I bladet dynamiske Data Masking i portalen kun du se de anbefalede kolonner for din database. Alt, du skal gøre, er på **Tilføj maske** for en eller flere kolonner og klik derefter på **Gem** for at anvende en maske for disse felter.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurere dynamiske data Maskering for databasen ved hjælp af portalen Azure

1. Start på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

2. Gå til indstillinger for blade af den database, der indeholder de følsomme data, du vil beskære.

3. Klik på feltet **Dynamiske Data Masking** som åbner bladet **Dynamiske Data Masking** .

    * Alternativt kan du rulle ned til sektionen **Handlinger** og klikke på **Dynamiske Data Masking**.

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. I bladet **Dynamiske Data Masking** kan du se nogle databasekolonner er, der er afmærket anbefalinger program til Maskering. For at acceptere anbefalingerne, skal du klikke på blot **Tilføj maske** for en eller flere kolonner og en maske oprettes ud fra standardtypen for denne kolonne. Du kan ændre funktionen Maskering ved at klikke på reglen, Maskering og redigere afmaskningen feltformat til et andet format efter eget valg. Husk at klikke på **Gem** for at gemme dine indstillinger.

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Hvis du vil føje en inputmaske til en kolonne i databasen, øverst i bladet **Dynamiske Data Masking** skal du klikke på **Tilføj maske** for at åbne bladet **Tilføj Masking regel** konfiguration

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Vælg den **skema**, en **tabel** og en **kolonne** til at definere det angivne felt, der vil blive maskeret.

7. Vælg et **Masking feltformat** på listen over følsomme data masking kategorier.

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>     

8. Klik på **Gem** i dataene masking regel blade for at opdatere sæt af masking regler i de dynamiske data masking politik.

9. Skriv SQL-brugere eller AAD identiteter, der skal udelukkes fra Maskering, og har adgang til de uafmaskede følsomme data. Dette skal være en adskilt af semikolon liste over brugere. Bemærk, at brugere med administratorrettigheder altid har adgang til de oprindelige umaskeret data.

    ![Navigationsruden](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    >[AZURE.TIP] For at gøre det, så programlag kan vise følsomme data til følsomme brugere, skal du tilføje SQL-brugeren eller AAD identitet programmet bruger til at forespørge databasen. Det anbefales, at denne liste indeholder et minimale antal privilegerede brugere at minimere visning af de følsomme data.

10. Klik på **Gem** i dataene masking konfiguration blade for at gemme den nye eller opdaterede Maskering politik.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurere dynamiske data masking for databasen ved hjælp af Powershell-cmdlet'er

Se [Azure SQL Database-cmdletter](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurere dynamiske data Maskering for databasen ved hjælp af REST-API

Få vist [operationer til Azure SQL-databaser](https://msdn.microsoft.com/library/dn505719.aspx).
