<properties
   pageTitle="Kryptering af gennemsigtig Data i SQL datawarehouse (Portal) | Microsoft Azure"
   description="Gennemsigtige datakryptering (TDE) i SQL datawarehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Komme i gang med gennemsigtig Data kryptering (TDE) i SQL Data Warehouse

> [AZURE.SELECTOR]
- [Oversigt over sikkerhed](sql-data-warehouse-overview-manage-security.md)
- [Godkendelse](sql-data-warehouse-authentication.md)
- [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
- [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Nødvendige tilladelser

For at aktivere gennemsigtig Data kryptering (TDE), skal du være administrator eller medlem af rollen dbmanager.

## <a name="enabling-encryption"></a>Aktivering af kryptering

Følg nedenstående trin for at aktivere TDE for et SQL Data Warehouse skal:

1. Åbn databasen med [Azure-portalen](https://portal.azure.com)
2. Klik på knappen **Indstillinger** i bladet database
3. Vælg indstillingen **gennemsigtig datakryptering**![][1]
4. Vælg indstillingen **på**![][2]
5. Vælg **Gem**
![][3]  

## <a name="disabling-encryption"></a>Deaktivere kryptering

Følg nedenstående trin for at deaktivere TDE for et SQL Data Warehouse skal:

1. Åbn databasen med [Azure-portalen](https://portal.azure.com)
2. Klik på knappen **Indstillinger** i bladet database
3. Vælg indstillingen **gennemsigtig datakryptering**![][1]
4. Vælg indstillingen **fra**![][4]
5. Vælg **Gem**
![][5]  

## <a name="encryption-dmvs"></a>Kryptering DMVs

Kryptering kan bekræftes med følgende DMVs:

- [sys.databases]
- [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
