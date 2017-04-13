<properties 
   pageTitle="Advisor til Azure SQL-Database ved hjælp af portalen Azure | Microsoft Azure" 
   description="Du kan bruge Azure SQL Database Advisor i portalen Azure Gennemse og implementere anbefalinger til din eksisterende SQL-databaser, der kan forbedre Forespørgselsydeevnen for aktuelle." 
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
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>SQL-Database Advisor ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [SQL-Database Advisor oversigt](sql-database-advisor.md)
- [Portal](sql-database-advisor-portal.md)

Du kan bruge Azure SQL Database Advisor i portalen Azure Gennemse og implementere anbefalinger til din eksisterende SQL-databaser, der kan forbedre Forespørgselsydeevnen for aktuelle.

## <a name="viewing-recommendations"></a>Få vist anbefalinger

Siden anbefalinger er, hvor du få vist de øverste anbefalinger, der er baseret på deres potentielle virkning til at forbedre ydeevnen. Du kan også få vist status for de historiske handlinger. Vælg en anbefaling eller status for at få vist flere detaljer.

For at få vist og anvende anbefalinger, skal have du tilladelsesniveauet korrekte [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md) i Azure. **Læser** **SQL DB bidragyder** tilladelser er påkrævet for at få vist anbefalinger og **ejer**, **SQL DB bidragyder** tilladelser der kræves for at udføre en hvilken som helst handlinger oprette eller slip indeks og annullere oprettelse af indeks.

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **flere tjenester** > **SQL-databaser**, og vælg databasen.
5. Klik på **ydeevne anbefaling** for at få vist tilgængelige anbefalinger for den valgte database.

> [AZURE.NOTE] For at få anbefalinger en database, skal have om en dag i brugen, og der skal være nogle aktivitet. Der også skal være nogle ensartet aktivitet. SQL-Database Advisor kan nemmere optimere for ensartet forespørgsel mønstre, end det kan for tilfældige spotty indstilling aktivitet. Hvis anbefalinger ikke er tilgængelige, kan siden **ydeevne anbefaling** give en meddelelse, der forklarer, hvorfor.

![Anbefalinger](./media/sql-database-advisor-portal/recommendations.png)

Her er et eksempel på "Løs skema problem" anbefaling i portalen Azure.

![Løse skema problem](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Anbefalinger er sorteret efter deres potentielle påvirkning af ydeevnen i følgende fire kategorier:

| Virkning | Beskrivelse |
| :--- | :--- |
| Høj | Høj virkning anbefalinger kan give den største indflydelse på ydeevnen. |
| Mediet | Mellemlangt virkning anbefalinger skal forbedre ydeevnen, men ikke væsentligt. |
| Lav | Lav virkning anbefalinger kan give bedre ydeevne end uden, men forbedringer muligvis ikke betydeligt. 


### <a name="removing-recommendations-from-the-list"></a>Fjerne anbefalinger på listen

Hvis din liste over anbefalinger indeholder elementer, du vil fjerne fra listen, kan du slette anbefalingen:

1. Vælg en anbefaling på listen over **anbefalinger**.
2. Klik på **Slet** i bladet **Detaljer** .


Hvis du vil, kan du tilføje kasseret elementer tilbage til listen **anbefalinger** :

1. Klik på **Vis kasseret**bladet **anbefalinger** .
1. Vælg en kasseret element på listen for at få vist detaljerne.
1. Du kan også klikke på **Fortryd Slet** for at indsætte indekset tilbage til den overordnede liste med **anbefalinger**.



## <a name="applying-recommendations"></a>Anvende anbefalinger

SQL-Database Advisor giver dig fuld kontrol over, hvordan anbefalinger er aktiveret ved hjælp af følgende tre indstillinger: 

- Anvende individuelle anbefalinger én ad gangen.
- Aktivere advisor for automatisk at anvende anbefalinger (aktuelt gælder kun indeks anbefalinger).
- For at implementere en anbefaling manuelt, ved at køre scriptet anbefalede T-SQL mod databasen.

Vælg ingen anbefalinger til at se detaljerne og derefter klikke på **Vis script** for at få vist de præcise detaljer om, hvordan anbefalingen er oprettet.

Databasen forbliver online mens advisor gælder anbefaling – brug af SQL-Database Advisor aldrig tager en database offline.

### <a name="apply-an-individual-recommendation"></a>Anvende en individuelle anbefaling

Du kan gennemgå og acceptere anbefalinger én ad gangen.

1. Klik på en anbefaling bladet **anbefalinger** .
2. Klik på **Anvend**på bladet **Detaljer** .

    ![Anvende anbefaling](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Aktivere automatiske indeks administration

Du kan angive SQL-Database Advisor at implementere anbefalinger automatisk. Efterhånden som anbefalinger bliver tilgængelige anvendes de automatisk. Som med alle indeks handlinger, der administreres af tjenesten, hvis ydeevnen betydning er negative slettes anbefalingen.

1. Klik på **automatisere**bladet **anbefalinger** :

    ![Advisor indstillinger](./media/sql-database-advisor-portal/settings.png)

2. Angive advisor automatisk **oprette** eller **Slip** indeks:

    ![Anbefalede indeks](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Køre manuelt de anbefalede T-SQL-script

Vælg ingen anbefalinger, og klik derefter på **Vis script**. Kør dette script i forhold til databasen til at anvende anbefalingen manuelt.

*Indeks, der udføres manuelt ikke overvåges og godkendt til indflydelse på ydeevnen af tjenesten* , så det anbefales, at du overvåge disse indeks efter oprettelse af at bekræfte de giver ydeevne og justere eller slette dem, hvis det er nødvendigt. Du kan finde oplysninger om oprettelse af indeks, [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>Annullere anbefalinger

Anbefalinger, der findes i statussen **Afventer**, **kontrollerer**eller **succes** kan annulleres. Anbefalinger med statussen af **Executing** kan ikke annulleres.

1. Vælg en anbefaling i området **Tilpasning oversigt** til at åbne bladet **anbefalinger detaljer** .
2. Klik på **Annuller** for at afbryde processen med at anvende anbefalingen.



## <a name="monitoring-operations"></a>Overvågning af handlinger

Anvende en anbefaling muligvis ikke sker med det samme. Portalen indeholder oplysninger om status for anbefaling handlinger. Følgende er mulige tilstande, som kan være et indeks:

| Status | Beskrivelse |
| :--- | :--- |
| Ventende | Anvende anbefaling kommandoen er modtaget og er planlagt til udførelse af. |
| Afholde | Anbefalingen anvendes. |
| Succes | Anbefaling blev anvendt. |
| Fejl | Der opstod en fejl under processen med at anvende anbefalingen. Det kan være et midlertidigt problem eller muligvis et skema ændre til tabellen og scriptet er ikke længere gyldig. |
| Hvis du vender tilbage | Anbefalingen blev anvendt, men er blevet anses for ikke-performant og gendannes automatisk. |
| Gendannes | Anbefalingen blev gendannes. |

Klik på en igangværende anbefaling fra listen til at se flere oplysninger:

![Anbefalede indeks](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Gendanne en anbefaling

Hvis du brugte advisor til at anvende anbefalingen gendannes (hvilket betyder, at du ikke manuelt køre T-SQL-script) den automatisk den Hvis der bliver fundet den ydeevne betydning for negativ. Hvis en eller anden grund du blot vil gendanne en anbefaling kan du gøre følgende:


1. Vælg en korrekt anvendte anbefaling i området **optimering af oversigt** .
2. Klik på **Genindlæs** i bladet **anbefaling detaljer** .

![Anbefalede indeks](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Overvågning af ydeevnen gennemslagskraft indeks anbefalinger

Når anbefalinger er korrekt implementeret (i øjeblikket indeksere handlinger og standardelementet forespørgsler anbefalinger kun) du kan klikke på **Forespørgsel indsigt** på bladet anbefaling oplysninger til at åbne [Forespørgsel ydeevne indsigt](sql-database-query-performance.md) og se ydeevne effekten af de mest populære forespørgsler.

![Overvåge ydeevnen virkning](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Oversigt

SQL-Database Advisor indeholder anbefalinger til forbedring af ydeevnen i SQL-database. Ved at indsende T-SQL-scripts, samt individuelle og fuldt-automatisk (i øjeblikket kun indeks), indeholder advisor nyttige hjælp til optimering af din database og i sidste ende forbedring af Forespørgselsydeevnen.



## <a name="next-steps"></a>Næste trin

Overvåge dine anbefalinger, og fortsæt med at anvende dem for at forbedre ydeevnen. Database arbejdsmængder er dynamiske og ændre løbende. SQL-Database advisor fortsætter med at overvåge og give anbefalinger, der potentielt kan forbedre ydeevnen i din database. 

 - Du kan finde en oversigt over SQL Database Advisor i [SQL Database Advisor](sql-database-advisor.md) .
 - Se [Forespørgslen ydeevne indsigt](sql-database-query-performance.md) til at få mere at vide om visning af ydeevnen påvirkningen af din mest populære forespørgsler.

## <a name="additional-resources"></a>Yderligere ressourcer

- [Forespørgsel Store](https://msdn.microsoft.com/library/dn817826.aspx)
- [OPRETTE INDEKS](https://msdn.microsoft.com/library/ms188783.aspx)
- [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md)






