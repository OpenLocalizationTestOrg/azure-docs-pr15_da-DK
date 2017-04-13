<properties 
    pageTitle="Ændre service niveau og ydeevne niveauet for en Azure SQL-database ved hjælp af PowerShell | Microsoft Azure" 
    description="Ændre webtjenesten og præstationsniveau Azure SQL-database viser, hvordan du skalere SQL-database op eller ned med PowerShell. Ændre det priser niveau i en Azure SQL-database med PowerShell." 
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


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database med PowerShell


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Tjenesten niveauer og ydeevne beskrives de funktioner og ressourcer tilgængelige for din SQL-database og kan blive opdateret, som behovet i dit program ændring. Yderligere oplysninger finder du [Service niveauer](sql-database-service-tiers.md).

Bemærk, at ændre webtjenesten og/eller præstationsniveau en database opretter en kopi af den oprindelige database på niveauet for nye ydeevne og derefter skifter forbindelser til replikaen. Ingen data går tabt under denne proces, men under det korte øjeblik, når vi skifte til replikaen, forbindelser til databasen er deaktiveret, så nogle transaktioner i flight kan annulleres. Dette vindue varierer, men er gennemsnitligt under 4 sekunder, og i mere end 99% af sager er mindre end 30 sekunder. Meget sjældent, især hvis der er stort antal transaktioner i flight på øjeblik forbindelserne er deaktiveret, dette vindue kan være længere.  

Varigheden af hele skalere op processen afhænger af på begge størrelse og service niveau af databasen, før og efter ændringen. For eksempel skal en 250 GB database, der ændres til, fra eller i en Standard webtjenesten fuldføres inden for 6 timer. Til en database med samme størrelse, er ved at ændre ydeevneniveauer i webtjenesten Premium, skal den fuldført inden for 3 timer.


- Hvis du vil nedgradere en database, skal databasen være mindre end den maksimale tilladte størrelse af webtjenesten mål. 
- Når du opgraderer en database med [Geografisk gentagelse](sql-database-geo-replication-portal.md) aktiveret, skal du først opgradere dens sekundær databaser til det ønskede ydeevne niveau før du opgraderer den primære database.
- Når nedgradere fra en Premium webtjenesten, skal du først afbryde alle geografisk gentagelse relationer. Du kan følge trinnene beskrevet i emnet [gendanne fra en afbrydelse](sql-database-disaster-recovery.md) til at stoppe processen til replikering mellem primært og sekundære aktive databaserne.
- Gendan-servicetilbud er forskellige for de forskellige dele af tjenesten. Hvis du nedgradere du muligvis mister muligheden for at gendanne til et tidspunkt, eller har en lavere sikkerhedskopiering opbevaringsperiode. Se [Azure SQL Database sikkerhedskopierings- og](sql-database-business-continuity.md)få mere at vide.
- De nye egenskaber for databasen anvendes ikke, før ændringerne er fuldført.



**Du skal bruge følgende for at fuldføre i denne artikel:**

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement skal du blot klikke på **Gratis konto** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- Azure SQL-database. Hvis du ikke har en SQL-database, kan du oprette én følge trinnene i denne artikel: [oprette din første Azure SQL-Database](sql-database-get-started.md).
- Azure PowerShell.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Ændre service niveau og ydeevne niveauet for SQL-database

Køre [AzureRmSqlDatabase sæt] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet og konfigurere **-RequestedServiceObjectiveName** til det ønskede priser niveau præstationsniveau f.eks *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Eksempel på en PowerShell-script for at ændre service niveau og ydeevne niveauet for SQL-database

Erstatte ```{variables}``` med dine-værdier (ikke du skal medtage klammeparenteser).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Næste trin

- [Skalere ind og ud](sql-database-elastic-scale-get-started.md)
- [Oprette forbindelse og forespørge på en SQL-database med SSMS](sql-database-connect-query-ssms.md)
- [Eksportere en Azure SQL-database](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Yderligere ressourcer

- [Oversigt over Business løbende](sql-database-business-continuity.md)
- [SQL-Database dokumentation](http://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database-cmdletter] (http://msdn.microsoft.com/library/azure/mt574084https :/ / msdn.microsoft.com/bibliotek/azure/mt619433(v=azure.300\).aspx.aspx)