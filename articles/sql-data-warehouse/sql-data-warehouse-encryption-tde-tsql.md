<properties
   pageTitle="Kryptering af gennemsigtig Data i SQL datawarehouse (T-SQL) | Microsoft Azure"
   description="Gennemsigtige datakryptering (TDE) i SQL datawarehouse (T-SQL)"
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

# <a name="get-started-with-transparent-data-encryption-tde"></a>Komme i gang med gennemsigtig Data kryptering (TDE)


> [AZURE.SELECTOR]
- [Oversigt over sikkerhed](sql-data-warehouse-overview-manage-security.md)
- [Godkendelse](sql-data-warehouse-authentication.md)
- [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
- [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Nødvendige tilladelser

For at aktivere gennemsigtig Data kryptering (TDE), skal du være administrator eller medlem af rollen dbmanager.

## <a name="enabling-encryption"></a>Aktivering af kryptering

Følg disse trin for at aktivere TDE for et SQL Data Warehouse:

1. Oprette forbindelse til *master* databasen på den server, der er vært for databasen ved hjælp af et logon, der er en administrator eller medlem af rollen **dbmanager** i den overordnede database
2. Udfør følgende sætning for at kryptere databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Deaktivere kryptering

Følg disse trin for at deaktivere TDE for et SQL Data Warehouse:

1. Oprette forbindelse til den *overordnede* database ved hjælp af et logon, der er en administrator eller medlem af rollen **dbmanager** i den overordnede database
2. Udfør følgende sætning for at kryptere databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] En midlertidigt afbrudt SQL Data Warehouse skal genoptages, før du ændrer indstillingerne for TDE.

## <a name="verifying-encryption"></a>Bekræfte kryptering

Følg nedenstående trin for at bekræfte kryptering status for et SQL Data Warehouse:

1. Oprette forbindelse til den *overordnede* eller forekomst database ved hjælp af et logon, der er en administrator eller medlem af rollen **dbmanager** i den overordnede database
2. Udfør følgende sætning for at kryptere databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Et resultat af ```1``` angiver en krypteret database ```0``` angiver en ikke-krypteret database.

## <a name="encryption-dmvs"></a>Kryptering DMVs  

- [sys.databases][] 
- [sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
