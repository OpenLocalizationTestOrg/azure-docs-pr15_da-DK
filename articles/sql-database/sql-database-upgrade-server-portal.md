<properties
    pageTitle="Opgradering til version 12 Azure SQL-Database ved hjælp af portalen Azure | Microsoft Azure"
    description="Beskriver, hvordan du opgradere til Azure SQL-Database version 12 herunder hvordan du kan opgradere Web og Business databaser, og hvordan du opgradere en V11 server overføre dens databaser direkte i en elastiske database puljen, ved hjælp af portalen Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Opgradering til version 12 Azure SQL-Database ved hjælp af portalen Azure


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL-Database version 12 er den nyeste version, så opgradering eksisterende servere til SQL-Database version 12 anbefales.
SQL-Database version 12 har mange [fordele i forhold til den tidligere version](sql-database-v12-whats-new.md) , herunder:

- Øget kompatibilitet med SQL Server.
- Forbedret premium ydeevne og nye ydeevneniveauer.
- [Elastiske database grupper](sql-database-elastic-pool.md).

Denne artikel indeholder anvisninger til opgradering af eksisterende SQL-Database V11 servere og databaser til version 12 SQL-Database.

Under processen med at opgradere til version 12 opgraderer du en hvilken som helst Web og Business databaser til en ny webtjenesten så kørselsvejledning til opgradering af websted og Business databaser er inkluderet.

Derudover kan kan overføre til en [elastiske database puljen](sql-database-elastic-pool.md) være mere rentable end opgradering til individuel performanceniveauer (priser niveauer) for enkelt databaser. Grupper forenkle også database management, fordi du skal kun bruge til at administrere indstillingerne for puljen i stedet for at administrere separat ydeevneniveauer af individuelle databaser ydeevne. Hvis du har databaser på flere servere kan du overveje at flytte dem til den samme server, og at drage fordel af at placere dem i en gruppe. Du kan nemt [overføre databaser fra V11 servere direkte i elastiske database grupper ved hjælp af PowerShell](sql-database-upgrade-server-powershell.md). Du kan også bruge portalen overføre V11 databaser til en gruppe, men i portalen skal du allerede har en version 12-serveren for at oprette en gruppe. Kørselsvejledning leveres senere i denne artikel for at oprette puljen efter serveropgraderingen, hvis du har [databaser, der kan få glæde af en gruppe](sql-database-elastic-pool-guidance.md).

Bemærk, at dine databaser skal forblive online og fortsætte med at arbejde i hele handlingen opgradering. Kan forekomme i en meget lille varighed, der typisk omkring 90 sekunder, men kan være op til 5 minutter på tidspunktet for den faktiske overgang til det nye ydeevneniveau midlertidige slippe af forbindelser til databasen. Hvis dit program har [midlertidige fejl håndtering af for forbindelse afslutninger](sql-database-connectivity-issues.md) er det tilstrækkelig til at beskytte mod mistede forbindelser i slutningen af opgraderingen.

Opgradere til SQL-Database version 12 kan ikke fortrydes. Serveren gendannes ikke til V11 efter en opgradering.

Efter opgradering til version 12, kan [service niveau anbefalinger](sql-database-service-tier-advisor.md) og [elastiske puljen overvejelser](sql-database-elastic-pool-guidance.md) umiddelbart ikke tilgængelige indtil tjenesten har tid til at evaluere din arbejdsbelastninger på den nye server. Oversigt over V11 server anbefaling gælder ikke for version 12-servere, så det ikke bevares.

## <a name="prepare-to-upgrade"></a>Forberede dig på at opgradere

- **Opgradere databaser for alle Web og Business**: Se sektionen nedenfor, eller se [opgradere databaser for alle Web og Business](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) [skærm og administrere en elastiske database puljen (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Gennemse og annullere geografisk gentagelse**: Hvis din Azure SQL-database er konfigureret til geografisk gentagelse du bør dokumentere dens aktuelle konfiguration og [stop geografisk gentagelse](sql-database-geo-replication-portal.md#remove-secondary-database). Omkonfigurere databasen til geografisk replikering, når opgraderingen er fuldført.
- **Åbn følgende porte, hvis du har klienter på en Azure VM**: Hvis klientprogrammet opretter forbindelse til SQL-Database version 12, mens din klient kører på en Azure VM (virtual machine), skal du åbne portområder 11000 11999 og 14000-14999 på VM. Yderligere oplysninger finder du [porte til version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Starte opgraderingen

1. [Azure portal](https://portal.azure.com/) Søg på serveren du vil opgradere ved at vælge **Gennemse** > **SQL-servere**og vælge den ønskede at opgradere v2.0 server.
2. Vælg **Seneste SQL Database-opdatering**og derefter **opgradere denne server**.

      ![opgradere server][1]

3. Opgradering af en server til den nyeste opdatering til SQL-Database er permanent og ikke kan fortrydes. Skriv navnet på din server for at bekræfte opgraderingen, og klik på **OK**.

## <a name="upgrade-all-web-and-business-databases"></a>Opgradere alle Web og Business databaser

Hvis din server har et websted eller Business-databaser skal du opgradere dem. Du vil opdatere alle Web og Business databaser til en ny webtjenesten under processen med at opgradere til version 12 SQL-Database.    

For at hjælpe dig med at opgradere, anbefaler tjenesten SQL-Database relevante service niveau og ydeevne niveauet (priser niveau) for hver database. Tjenesten anbefaler det bedste niveau for at køre din eksisterende database arbejdsbelastningen ved at analysere historiske forbrug for din database.

3. Vælg hver database til at gennemse og vælge de anbefalede priser klasse for at opgradere til i bladet **opgradere denne server** . Du kan altid gennemse de tilgængelige priser lag og vælge det, der passer bedst til dit miljø.


     ![databaser][2]


7. Når du klikker på det foreslåede niveau vises med bladet **Vælg din priser niveau** , hvor du kan vælge et niveau og derefter klikke på knappen **Vælg** til at ændre til dette niveau. Vælg et nyt trin for hver Web- eller Business-database.

    Hvad er vigtigt at bemærke er, at din SQL-database ikke er låst til en bestemt service niveau eller ydeevne-niveau, så som kravene i din database ændring kan du nemt ændre mellem de tilgængelige service niveauer og ydeevne. Faktisk Basic, som Standard og Premium SQL-databaser faktureret ved timen, og du har mulighed for at skalere hver database op eller ned 4 gange i løbet 24 timer.

    ![anbefalinger][6]


Når alle databaser på serveren, der kan bruges er du klar til at starte opgraderingen

## <a name="confirm-the-upgrade"></a>Bekræfte opgraderingen

3. Når alle databaser på serveren er berettiget til opgradering skal du kontrollere, at du vil udføre opgraderingen, og klik derefter på **OK** **TYPE FELTET servernavn** .

    ![bekræfte opgraderingen][3]


4. Opgraderingen starter og viser de igangværende meddelelse. Opgraderingsprocessen startes. Afhængigt af oplysningerne om din specifikke databaser kan opgradering til version 12 tage noget tid. I denne periode alle databaser på serveren, forbliver online, men server og database management handlinger vil være begrænset.

    ![opgradering er i gang][4]

    Niveau midlertidige slippe af forbindelser til databasen på tidspunktet for faktisk overgangen til den nye ydeevne kan ske, til en meget lille varighed (typisk målt i sekunder). Hvis et program har håndtering af midlertidige fejl (forsøg) for forbindelse afslutninger, så det er tilstrækkelig til at beskytte mod mistede forbindelser i slutningen af opgraderingen.

5. Når opgraderingen er fuldført vises den **Seneste opdatering** blade **aktiveret**.

    ![Version 12 er aktiveret][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Flytte dine databaser til en elastiske database puljen

Gå til version 12 serveren [Azure-portalen](https://portal.azure.com/) og klik på **Tilføj puljen**.

- eller -

Hvis du får vist en meddelelse om **Klik her for at få vist de anbefalede elastiske database grupper for denne server**, skal du klikke på den, så du nemt oprette en gruppe, der er optimeret til din server-databaser. Yderligere oplysninger finder du [pris og ydeevne overvejelser i forbindelse med en elastiske database puljen](sql-database-elastic-pool-guidance.md).

![Tilføje puljen til en server][7]

Følg vejledningen i [oprette en elastiske database puljen](sql-database-elastic-pool.md) artiklen for at afslutte oprettelsen af din gruppe.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Overvåge databaser efter opgradering til SQL-Database version 12

>[AZURE.IMPORTANT] Opgradere til den nyeste version af SQL Server Management Studio (SSMS) at drage fordel af de nye version 12-funktioner. [Hent SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Efter opgradering, aktivt overvåge databasen for at sikre programmer kører på den ønskede ydeevne og derefter optimere indstillingerne efter behov.

Ud over overvågning individuelle databaser, kan du overvåge elastiske database grupper [skærm, administrere, og tilpasse størrelsen af en elastiske database puljen ved hjælp af Azure portal](sql-database-elastic-pool-manage-portal.md) eller med [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Forbrug ressourcedata:** For Basic, som Standard og Premium databaser er forbrug ressourcedata tilgængelig via den [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV i databasen. Denne DMV indeholder nær realtid forbrug ressourceoplysninger på 15 anden granularitet i den forrige time handling. DTU procentdel forbrug for et interval beregnes som den maksimale procentdel forbrug af CPU, EY og log målangivelserne. Her er en forespørgsel til at beregne det gennemsnitlige DTU procentdel forbrug over den seneste time:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Yderligere overvågning oplysninger:

- [Azure SQL-Database ydeevne vejledning til enkelt databaser](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Pris og ydeevne overvejelser i forbindelse med en elastiske database puljen](sql-database-elastic-pool-guidance.md).
- [Overvågning Azure SQL-Database ved hjælp af dynamiske management visninger](sql-database-monitoring-with-dmvs.md)




**Beskeder:** Konfigurere 'Beskeder' i portalen Azure til at give dig besked, når DTU forbruget for en opgraderet database nærmer sig visse højt niveau. Database påmindelser kan være konfiguration på portalen Azure på forskellige ydeevne målepunkter som DTU, CPU, EY og Log. Gå til din database, og vælg **regler for påmindelser** i bladet **Indstillinger** .

For eksempel kan du konfigurere en besked via mail på "DTU procent" Hvis procentdel gennemsnitsværdien DTU overstiger 75% over de sidste fem minutter. Referere til at [modtage beskeder om meddelelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) for at lære mere om at konfigurere påmindelser.





## <a name="next-steps"></a>Næste trin

- [Kontrollér, om ressourcepuljen anbefalinger og oprette en gruppe](sql-database-elastic-pool-create-portal.md).
- [Ændre niveauet for tjenesten niveau og ydeevnen af databasen](sql-database-scale-up.md).



## <a name="related-links"></a>Relaterede Links

- [Hvad er nyt i SQL-Database version 12](sql-database-v12-whats-new.md)
- [Planlægge og forberede dig på at opgradere til SQL-Database version 12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
