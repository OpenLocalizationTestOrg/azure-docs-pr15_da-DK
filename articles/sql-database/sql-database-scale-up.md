<properties
    pageTitle="Ændre service niveau og ydeevne niveauet for en Azure SQL-database | Microsoft Azure"
    description="Ændre webtjenesten og præstationsniveau Azure SQL-database viser, hvordan du skalere SQL-database, op eller ned. Ændre det priser niveau i en Azure SQL-database."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database ved hjælp af portalen Azure


> [AZURE.SELECTOR]
- [**Azure-portalen**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Tjenesten niveauer og ydeevne beskrives de funktioner og ressourcer tilgængelige for din SQL-database og kan blive opdateret, som behovet i dit program ændring. Yderligere oplysninger finder du [Service niveauer](sql-database-service-tiers.md).

Bemærk, at ændre webtjenesten og/eller præstationsniveau en database opretter en kopi af den oprindelige database på niveauet for nye ydeevne og derefter skifter forbindelser til replikaen. Ingen data går tabt under denne proces, men under det korte øjeblik, når vi skifte til replikaen, forbindelser til databasen er deaktiveret, så nogle transaktioner i flight kan annulleres. Dette vindue varierer, men er gennemsnitligt under 4 sekunder, og i mere end 99% af sager er mindre end 30 sekunder. Meget sjældent, især hvis der er stort antal transaktioner i flight på øjeblik forbindelserne er deaktiveret, dette vindue kan være længere.  

Varigheden af hele skalere op processen afhænger af på begge størrelse og service niveau af databasen, før og efter ændringen. For eksempel skal en 250 GB database, der ændres til, fra eller i en Standard webtjenesten fuldføres inden for 6 timer. Til en database med samme størrelse, er ved at ændre ydeevneniveauer i webtjenesten Premium, skal den fuldført inden for 3 timer.


Brug oplysningerne i [Azure SQL Database Service niveauer og ydeevne](sql-database-service-tiers.md) til at bestemme rette service niveau og ydeevne til Azure SQL-Database.

- Hvis du vil nedgradere en database, skal databasen være mindre end den maksimale tilladte størrelse af webtjenesten mål. 
- Når du opgraderer en database med [Geografisk gentagelse](sql-database-geo-replication-overview.md) aktiveret, skal du først opgradere dens sekundær databaser til det ønskede ydeevne niveau før du opgraderer den primære database.
- Når nedgradere et webtjenesten, skal du først afbryde alle geografisk gentagelse relationer. 
- Gendan-servicetilbud er forskellige for de forskellige dele af tjenesten. Hvis du nedgradere du muligvis mister muligheden for at gendanne til et tidspunkt, eller har en lavere sikkerhedskopiering opbevaringsperiode. Se [Azure SQL Database sikkerhedskopierings- og](sql-database-business-continuity.md)få mere at vide.
- Ændre databasen priser niveau ændrer ikke den maksimale databasestørrelse. Hvis du vil ændre databasen Brug Maks [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) eller [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- De nye egenskaber for databasen anvendes ikke, før ændringerne er fuldført.



**Du skal bruge følgende for at fuldføre i denne artikel:**

- Azure SQL-database. Hvis du ikke har en SQL-database, kan du oprette én følge trinnene i denne artikel: [oprette din første Azure SQL-Database](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Ændre niveauet for tjenesten niveau og ydeevnen af databasen


Åbn bladet SQL-Database til den database, du vil skalere op eller ned:

1.  Klik på **flere tjenester**i [Azure-portalen](https://portal.azure.com), > **SQL-databaser**.
2.  Klik på den database, du vil ændre.
3.  Klik på **priser niveau (skala DTUs)**på bladet **SQL-database** :

    ![priser niveau][1]

1.  Vælg et nyt lag, og klik på **Vælg**:

    Klik på **Vælg** sender en skala anmodning til at ændre det priser niveau. Afhængigt af størrelsen på databasen kan handlingen skala gå et stykke tid at fuldført (se oplysninger øverst i denne artikel).

    > [AZURE.NOTE] Ændre databasen priser niveau ændrer ikke den maksimale databasestørrelse. Hvis du vil ændre databasen Brug Maks [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) eller [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Vælg priser niveau][2]

3.  Klik på meddelelsesikonet (klokke) i øverste højre hjørne:

    ![beskeder][3]

    Klik på meddelelse teksten for at åbne detaljeruden, hvor du kan se statussen for din anmodning.




## <a name="next-steps"></a>Næste trin

- Ændre din database maksimal længde på ved hjælp af [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) eller [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Skalere ind og ud](sql-database-elastic-scale-get-started.md)
- [Oprette forbindelse og forespørge på en SQL-database med SSMS](sql-database-connect-query-ssms.md)
- [Eksportere en Azure SQL-database](sql-database-export.md)

## <a name="additional-resources"></a>Yderligere ressourcer

- [Oversigt over Business løbende](sql-database-business-continuity.md)
- [SQL-Database dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
