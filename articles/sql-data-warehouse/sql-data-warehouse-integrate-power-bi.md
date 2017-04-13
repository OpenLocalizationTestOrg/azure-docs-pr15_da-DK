<properties
   pageTitle="Bruge Power BI med SQL datawarehouse | Microsoft Azure"
   description="Tip til brug af Power BI med Azure SQL Data Warehouse til udvikling af løsninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-power-bi-with-sql-data-warehouse"></a>Bruge Power BI med SQL datawarehouse
Som med Azure SQL-Database SQL Data Warehouse Direct Connect giver brugeren mulighed at udnytte effektive logiske pushdown sammen med analysefunktionerne i Power BI.  Med Direct Connect sendes forespørgsler tilbage til Azure SQL Data Warehouse i realtid, som du udforske data.  Dette skal kombineres med skalaen på SQL Data Warehouse, kan brugere oprette dynamiske rapporter i minutter mod TB data.  Desuden kan indførelsen af Power BI-knappen Åbn i direkte forbindelse Power BI til deres SQL Data Warehouse uden at indsamle oplysninger fra andre dele af Azure-brugere.

Når du bruger Direct Connect skal du note:

+ Angiv fuldstændige servernavnet, når der oprettes forbindelse (se nedenfor for at få flere oplysninger)
+ Kontrollér firewallregler for databasen er konfigureret til "Tillad adgang til Azure services".
+ Hver handling som at markere en kolonne eller tilføje et filter beder direkte datawarehouse
+ Felter opdateres cirka hver 15 minutter (Opdater ikke behøver at være planlagt)
+ Q & A er ikke tilgængelig for Direct Connect datasæt
+ Skemaændringer hentes ikke automatisk
+ Alle forespørgsler, der er direkte forbindelse afbrydes efter 2 minutter

Disse begrænsninger og noter kan blive ændret, når vi fortsætter med at forbedre oplevelser. Trin til at forbinde er beskrevet nedenfor.  

## <a name="using-the-open-in-power-bi-button"></a>Ved hjælp af knappen "Åben i Power BI"
Den nemmeste måde at flytte mellem dine SQL Data Warehouse og Power BI er åben i Power BI-knappen. Denne knap kan du problemfrit begynder at oprette nye dashboards i Power BI.  

1.  At komme i gang gå til din forekomst af SQL Data Warehouse i portalen Azure klassisk.
2.  Klik på knappen 'Åbn i Power BI'.
3.  Hvis vi ikke kan logge dig direkte, eller hvis du ikke har en Power BI-konto, skal du logge på.  
4.  Du får adgang til siden SQL Data Warehouse forbindelse med oplysninger fra din SQL Data Warehouse udfyldt på forhånd.
5.  Når indtaste dine legitimationsoplysninger vil du være fuldt forbindelse til din SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Oprette forbindelse via Power BI-portalen
Brugere kan også oprette forbindelse til deres SQL Data Warehouse via Power BI-portalen ud over at bruge kommandoen Åbn i Power BI-knappen.

1.  Klik på 'Hent Data' nederst i navigationsruden.
2.  Vælg 'Databaser'.
3.  Vælg én gang 'Azure SQL Data Warehouse' på siden databaser, og klik derefter på 'Opret forbindelse'.
4.  Angiv de nødvendige forbindelsesoplysninger.  Dit servernavn og databasenavn kan findes på portalen Azure.
5.  Du kommer tilbage til den primære side i Power BI og når der oprettes en ny post under 'Datasæt' din forbindelse vises med navnet på din forekomst.  
6.   Du kan klikke på det nye datasæt for at undersøge alle de tabeller og visninger i databasen. Vælge en kolonne sender en forespørgsel tilbage til den kilde, dynamisk oprettelse af de visuelle elementer. Disse visuelle elementer kan være gemt i en ny rapport og fastgjort tilbage til dit dashboard.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
