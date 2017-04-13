<properties
   pageTitle="Aktivere gennemsigtig datakryptering (TDE) for SQL Server-Stræk Database på Azure TSQL | Microsoft Azure"
   description="Aktivere gennemsigtig datakryptering (TDE) for SQL Server-Stræk Database på Azure TSQL"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Aktivere gennemsigtig datakryptering (TDE) for Stræk databasen på Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Azure-portalen](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Gennemsigtige Data kryptering (TDE) hjælper med at beskytte mod trusler fra skadelig aktivitet ved at udføre realtid kryptering og dekryptering af den database, tilknyttede sikkerhedskopier og transaktionslogfiler på resten uden ændringer til programmet.

TDE krypterer opbevaring af en hel database ved hjælp af en symmetriske nøgle, kaldes krypteringsnøglen database. Krypteringsnøglen database er beskyttet af et indbygget servercertifikat. Den indbyggede servercertifikat er entydige for hver Azure server. Microsoft roterer automatisk certifikaterne mindst én gang 90 dage. Du kan finde en generel beskrivelse af TDE [Gennemsigtig Data kryptering (TDE)].

##<a name="enabling-encryption"></a>Aktivering af kryptering

Hvis du vil aktivere TDE for en Azure database, der gemme dataene overført fra en Stræk aktiverede SQL Server-database skal du gøre følgende ting:

1. Oprette forbindelse til *master* databasen på Azure serveren vært for databasen ved hjælp af et logon, der er en administrator eller medlem af rollen **dbmanager** i den overordnede database
2. Udfør følgende sætning for at kryptere databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>Deaktivere kryptering

For at deaktivere TDE for en Azure database, der gemmes dataene, der er overført fra en Stræk aktiverede SQL Server-database skal du gøre følgende ting:

1. Oprette forbindelse til den *overordnede* database ved hjælp af et logon, der er en administrator eller medlem af rollen **dbmanager** i den overordnede database
2. Udfør følgende sætning for at kryptere databasen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Bekræfte kryptering

For at bekræfte kryptering status for en Azure-database, der gemmes dataene, der er overført fra en Stræk aktiverede SQL Server-database, skal du gøre følgende ting:

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


<!--Anchors-->
[Kryptering af gennemsigtig Data (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
