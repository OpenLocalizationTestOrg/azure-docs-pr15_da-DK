<properties
   pageTitle="Administrere Beregn power i Azure SQL Data Warehouse (Azure portal) | Microsoft Azure"
   description="Azure portalen opgaver til at administrere beregne power. Skala beregne ressourcer ved at justere DWUs. Eller pause og CV beregne ressourcer for at gemme omkostninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Administrere Beregn power i Azure SQL Data Warehouse (Azure portal)

> [AZURE.SELECTOR]
- [Oversigt](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTEN](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Skala ydeevne ved at skalere ud beregne ressourcer og hukommelse til at overholde de ændrede krav for din arbejdsbyrde. Gem omkostninger ved at skalere tilbage ressourcer under ikke-spidsbelastning eller stoppe Beregn helt. 

Denne samling af opgaver bruger Azure portalen for at:

- Skala Beregn
- Pause Beregn
- CV Beregn

Finde flere oplysninger, [Administrer beregne oversigt][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Skala Beregn power

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Sådan ændrer du Beregn ressourcer:

1. Åbn [Azure-portalen][], Åbn databasen, og klik på **skala**.

    ![Klik på skala][1]

1. Flyt skyderen mod venstre eller højre for at ændre indstillingen DWU i bladet skala.

    ![Flyt skyderen][2]

1. Klik på **Gem**. Der vises en bekræftelsesmeddelelse. Klik på **Ja** for at bekræfte eller **uden** at annullere.

    ![Klik på Gem][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pause Beregn

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Indsætte en database:

1. [Azure-portalen][] og åbne databasen. Bemærk, at Status er **Online**. 

    ![Onlinestatus][6]

1. Hvis du vil annullere Beregn og hukommelse ressourcer skal du klikke på **Pause**, og klik derefter en bekræftelsesmeddelelse vises. Klik på **Ja** for at bekræfte eller **uden** at annullere.

    ![Bekræfte pause][7]

1. Mens SQL Data Warehouse starter databasen, er status **Pausing**.
2. Når status er **midlertidigt afbrudt**, handlingen Afbryd midlertidigt er færdig, og du er ikke længere blive opkrævet betaling for DWUs.

    ![Pause status][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV Beregn

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Genoptage en database:

1. [Azure-portalen][] og åbne databasen. Bemærk, at Status er **midlertidigt afbrudt**. 

    ![Pause database][4]

1. For at fortsætte databasen, klik på **Start**, og derefter vises en bekræftelsesmeddelelse. Klik på **Ja** for at bekræfte eller **uden** at annullere.

    ![Bekræfte CV][5]

1. Mens SQL Data Warehouse starter databasen, er status "Resuming".
2. Databasen er klar, når status er **online**.

    ![Onlinestatus][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Næste trin
Få mere at vide under [Oversigt over administration][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Oversigt over administration]: ./sql-data-warehouse-overview-manage.md
[Administrere Beregn oversigt]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure-portalen]: http://portal.azure.com/
