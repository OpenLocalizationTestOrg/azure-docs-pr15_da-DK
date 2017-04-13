<properties 
   pageTitle="Priser niveau anbefalinger til Azure SQL-Database" 
   description="Når du ændrer priser er niveauer i portalen Azure, priser niveau anbefalinger, forudsat at anbefalet det niveau, der er bedst egnet til kørsel af en eksisterende Azure SQL Database arbejdsbelastningen. Priser niveauer beskriver service niveau og ydeevne niveauet for en SQL-database." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>SQL-Database priser niveau anbefalinger

 Priser niveau anbefalinger anbefaler, at det service niveau og ydeevne niveau, der er bedst egnet til kørsel af en eksisterende Azure SQL database arbejdsbelastningen.

> [AZURE.NOTE] Priser niveau anbefalinger er kun tilgængelig for Web og Business databaser og elastiske database grupper – og er kun tilgængelig i [Azure-portalen](https://portal.azure.com/).


Få pris niveau anbefalinger under følgende opgaver:

- [Ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database](sql-database-scale-up.md)
- [Opgradere Azure SQL server til version 12](sql-database-upgrade-server-portal.md)
- Gå til din version 12-server. Se [SQL-Database priser niveau anbefalinger](sql-database-service-tier-advisor.md).
- [Oprette en elastiske database puljen](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Oversigt

Tjenesten SQL-Database analyserer aktuelle ydeevne og funktionskrav ved at vurdere historiske ressourceforbrug til en SQL-database. Den mindste acceptable webtjenesten bestemmes desuden baseret på størrelsen på databasen, og aktiveret [Forretningskontinuitet](sql-database-business-continuity.md) funktioner. 

Disse oplysninger er analysere og service niveau og ydeevne niveauet, der passer bedst egnet til kørsel af databasens typiske arbejdsbelastning og vedligeholde er det aktuelle funktionssæt anbefales.

- Tjenesten undersøger de forrige 15 til 30 dage i historiske data (Ressourceforbrug, databasestørrelse og databaseaktivitet) og udfører en sammenligning mellem mængden ressourcer consumed og faktisk begrænsninger på nuværende tidspunkt tilgængelig tjeneste niveauer og ydeevne.
- Data er analysere 15 anden intervaller og hvert interval resultset er kategoriseret i den eksisterende webtjenesten og ydeevneniveau, der er bedst egnet til håndtering af pågældende resultset arbejdsbyrde.
- Disse 15 andet eksempler lægges derefter til større 15 30-dages analysen, og det service niveau og ydeevne niveau, kan optimalt håndtere 95% af historiske arbejdsbelastningen anbefales.

### <a name="recommendations"></a>Anbefalinger

Baseret på brugen af din database, findes der i øjeblikket 2 kategorier af anbefalinger, der kan opstå:


| Anbefaling | Beskrivelse |
| :--- | :--- |
| Opgradere | Opgradere til en ny niveau. |
| Ikke tilgængelig | En database kræver en mindste arbejdsbyrde eller cirka 35 dage aktivitet. Der er ikke nok data til at angive en gyldig anbefaling. |

## <a name="getting-pricing-tier-recommendations"></a>Få priser niveau anbefalinger

Få priser niveau anbefalinger ved at vælge en eksisterende database i internettet eller Business, skal du klikke på **alle indstillinger**, og klik derefter på **priser niveau (skala DTUs)**. (Priser niveau anbefalinger er også tilgængelige, når du [opgraderer Azure SQL server, der skal version 12](sql-database-upgrade-server-portal.md).)

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Gennemse** > **SQL-databaser**.
4. Klik på den database, du vil se en anbefaling for i bladet **SQL-databaser** :

    ![Vælg database][1]

5. Vælg **Indstillinger for alle** derefter **priser niveau (skala DTUs)**på bladet database.


7. **Anbefalede priser niveauer** Åbn, hvor du kan klikke på det foreslåede niveau og derefter klikke på knappen **Vælg** til at ændre til dette niveau.

    ![Tilmelde sig eksempel][4]

8. Du kan også klikke på **Vis detaljer om anvendelse** for at åbne bladet **Priser niveau anbefaling detaljer** , hvor du kan få vist det anbefalede niveau for databasen, en sammenligning af funktioner mellem aktuelle og anbefalede niveauer og en graf over besøgsanalyse historiske ressource.

    ![Tilmelde sig eksempel][5]



## <a name="summary"></a>Oversigt

Priser niveau anbefalinger give en automatiseret oplevelse for indsamling af telemetridata for hver SQL-database og anbefale den bedste service niveau og ydeevne niveau kombination baseret på en database faktisk ydeevne behov og funktionskrav. Klik på **priser niveau (skala DTUs)** for at få vist priser niveau anbefalinger til en hvilken som helst Web og Business databaser bladet indstillinger.



## <a name="next-steps"></a>Næste trin

Afhængigt af oplysninger om en bestemt database skal sker udføre en opgradering eller nedgraderingsrettigheder normalt ikke med det samme. På portalen give meddelelser som database overgange til dets nye niveau, eller du kan overvåge opgraderingsstatus ved at forespørge visningen [sys.dm_operation_status (Azure SQL-Database)](https://msdn.microsoft.com/library/dn270022.aspx) i SQL-Database serverens master databasen.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
