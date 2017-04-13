<properties 
   pageTitle="Azure SQL Database ydeevne indsigt | Microsoft Azure" 
   description="Azure SQL-Database indeholder ydeevne værktøjer for at hjælpe dig med at identificere områder, der kan forbedre Forespørgselsydeevnen for aktuelle." 
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
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>SQL-Database ydeevne indsigt

Azure SQL-Database indeholder ydeevne værktøjer for at hjælpe dig med at identificere og forbedre ydeevnen for dine databaser ved at give intelligent justering handlinger og anbefalinger. 

1. Gå til din database på [Azure-portalen](http://portal.azure.com) , og klik på **Indstillinger for alle** > **ydeevne **  >  **Oversigt** til at åbne siden **ydeevne** . 


2. Klik på **anbefalinger** for at åbne [SQL Database Advisor](#sql-database-advisor), og klik på **forespørgsler** for at åbne [Forespørgsel ydeevne indsigt](#query-performance-insight).

    ![Få vist ydeevne](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Oversigt over ydeevne

At klikke på **Oversigt over** eller under feltet **ydeevne** , fører dig til ydeevne dashboard for din database. Denne visning indeholder en oversigt over databasens ydeevne og hjælper dig med at med tilpasning af ydeevnen og fejlfinding. 

![Ydeevne](./media/sql-database-performance/performance.png)

- Feltet **anbefalinger** indeholder en opdeling af justering af anbefalinger til din database (top 3 anbefalinger er vist, hvis der er flere). Klik på dette felt, fører dig til **SQL Database Advisor**. 
- Feltet **optimering af aktivitet** indeholder en oversigt over igangværende og afsluttede justering af handlinger for databasen, hvorved du får en hurtig oversigt i oversigten over justering af aktivitet. Klik på dette felt, fører dig til fuld justering historik visning for din database.
- Feltet **justering af automatisk** viser sektionen justering af automatisk konfiguration for databasen (hvilke justering handlinger er konfigureret til at anvendes automatisk på databasen). Klik på dette felt åbnet dialogboksen automatisering konfiguration.
- Feltet **databaseforespørgsler** viser opsummeringen af forespørgselsydelse for databasen (overordnede DTU brugen og toppen ressource forbrug forespørgsler). Klik på dette felt, fører dig til **Forespørgsel ydeevne indsigt**.



## <a name="sql-database-advisor"></a>SQL-Database Advisor


[SQL Database Advisor](sql-database-advisor.md) indeholder intelligent justering anbefalinger, der kan hjælpe med at forbedre ydeevnen i din database. 

- Anbefalinger på som indekserer til at oprette eller slip (og mulighed for at anvende indeks anbefalinger automatisk uden brugerinput og automatisk gendannelse anbefalinger, der har en negativ indvirkning på ydeevnen).
- Anbefalinger når skema problemer identificeres i databasen.
- Anbefalinger når forespørgsler kan få glæde af forespørgsler med parametre.




## <a name="query-performance-insight"></a>Forespørgsel ydeevne indsigt

[Forespørgsel ydeevne indsigt](sql-database-query-performance.md) gør det muligt at bruge mindre tid på fejlfinding databasens ydeevne ved at indsende:

- Bedre indsigt i dine databaser ressourceforbrug (DTU). 
- Øverste CPU'EN forbrug forespørgsler, der kan potentielt optimeret til forbedret ydeevne. 
- Muligheden for at analysere ned i detaljerne for en forespørgsel. 


## <a name="additional-resources"></a>Yderligere ressourcer

- [Azure SQL-Database ydeevne vejledning til enkelt databaser](sql-database-performance-guidance.md)
- [Hvornår skal en elastiske database puljen bruges?](sql-database-elastic-pool-guidance.md)