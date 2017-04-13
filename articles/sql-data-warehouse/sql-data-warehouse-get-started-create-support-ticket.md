<properties
   pageTitle="Sådan oprettes en supportbilletter til SQL Data Warehouse | Microsoft Azure"
   description="Sådan oprettes en supportbilletter i Azure SQL-datalager."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Sådan oprettes en supportbilletter til SQL Data Warehouse
 
Hvis du har problemer med din SQL Data Warehouse, skal du oprette en supportbilletter så vores engineering-teamet kan hjælpe dig.

## <a name="create-a-support-ticket"></a>Oprette en supportbilletter

1. Åbn [Azure-portalen][].

2. Klik på feltet **Hjælp + support** på startskærmen.

    ![Hjælp + support](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. På siden Hjælp + Support blade, skal du klikke på **Opret supportanmodning**.

    ![Ny anmodning om support](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Vælg den **anmode om Type**.

    ![Anmode om type](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Som standard har hver SQL server (fx myserver.database.windows.net) et **DTU kvote** på 45,000. Denne kvote er blot en safety grænse. Du kan øge din kvote ved oprettelse af en supportbilletter og vælge *kvote* anmodning om filtype. Til at beregne din DTU skal, multiplicere 7.5 med totalen [DWU][] det er nødvendigt. For eksempel, du vil gerne være vært for to DW6000s på en SQL server, og derefter skal du anmode om en DTU kvote af 90,000.  Du kan se din aktuelle DTU forbrug fra bladet SQL server på portalen. Både er stoppet midlertidigt og ophævelse standsede databaser tælle mod kvoten DTU. 

5. Vælg det **abonnement** , der er vært databasen med det problem, du rapporterer.

    ![Abonnement](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Vælg **SQL datawarehouse** som ressourcen.

    ![Ressource](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Vælg din [Azure understøtter plan][].

    - Understøttelse af **fakturerings-kvote og abonnement** er tilgængelig på alle niveauer af support.
    - Understøttelse af **sideskift fix** leveres via [udvikler][], [Standard][], [Professionel direkte][] eller [Premier][] support. Sideskift løs problemer er kunder problemer under brug af Azure hvor der ikke er et begrundet forventningen, Microsoft forårsagede problemet.
    - **Udvikler vejledning** og **vejledende tjenester** er tilgængelige på [Professional direkte][] og [Premier][] support niveauer. 
    
    Hvis du har en Premier support plan, kan du også rapportere SQL Data Warehouse relaterede problemer under [Microsoft Premier online portal][].  Se [Azure understøtter planer][Azure understøtter plan] til at få mere for at vide om de forskellige support-planer, herunder omfang, svar gange, priser, osv.  Ofte stillede spørgsmål om Azure understøtter, finder du [Azure understøtter ofte stillede spørgsmål][].  

    ![Understøttelse af plan](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Vælg **problemtype** og **kategori**.

    ![Problem type kategori](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Beskriv problemet, og Vælg niveauet i virksomheden.

    ![Beskrivelse af problem](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Dine **kontaktoplysninger** for denne supportbilletter er allerede udfyldt. Opdater dette, hvis det er nødvendigt.

    ![Kontaktoplysninger](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Klik på **Opret** for at sende anmodning om support.


## <a name="monitor-a-support-ticket"></a>Overvåge en supportbilletter

Når du har sendt supportanmodning, vil Azure supportteamet kontakte dig. For at kontrollere din anmodning om status og detaljer, skal du klikke på **administrere anmodninger om support** på dashboardet.

![Kontrollere status](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Andre ressourcer

Desuden kan du oprette forbindelse til SQL Data Warehouse community'et på [Stakoverløb][] eller på [Azure SQL Data Warehouse MSDN-forum][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Azure-portalen]: https://portal.azure.com/
[Plan for Azure support]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Udvikler]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professionelt direkte]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support ofte stillede spørgsmål]: https://azure.microsoft.com/support/faq/
[Microsoft Premier onlineportalen]: https://premier.microsoft.com/
[Stakoverløb]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

