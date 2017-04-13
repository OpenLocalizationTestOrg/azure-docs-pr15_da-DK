<properties
   pageTitle="Aktivere gennemsigtig datakryptering (TDE) for SQL Server-Stræk Database på Azure | Microsoft Azure"
   description="Aktivere gennemsigtig datakryptering (TDE) for SQL Server-Stræk Database på Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Aktivere gennemsigtig datakryptering (TDE) for Stræk databasen på Azure
> [AZURE.SELECTOR]
- [Azure-portalen](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Gennemsigtige Data kryptering (TDE) hjælper med at beskytte mod trusler fra skadelig aktivitet ved at udføre realtid kryptering og dekryptering af den database, tilknyttede sikkerhedskopier og transaktionslogfiler på resten uden ændringer til programmet.

TDE krypterer opbevaring af en hel database ved hjælp af en symmetriske nøgle, kaldes krypteringsnøglen database. Krypteringsnøglen database er beskyttet af et indbygget servercertifikat. Den indbyggede servercertifikat er entydige for hver Azure server. Microsoft roterer automatisk certifikaterne mindst én gang 90 dage. Du kan finde en generel beskrivelse af TDE [Gennemsigtig Data kryptering (TDE)].

##<a name="enabling-encryption"></a>Aktivering af kryptering

Hvis du vil aktivere TDE for en Azure database, der gemme dataene overført fra en Stræk aktiverede SQL Server-database skal du gøre følgende ting:

1. Åbn databasen med [Azure-portalen](https://portal.azure.com)
2. Klik på knappen **Indstillinger** i bladet database
3. Vælg indstillingen **gennemsigtig datakryptering**![][1]
4. Vælg indstillingen **på** , og vælg derefter **Gem**
![][2]


##<a name="disabling-encryption"></a>Deaktivere kryptering

For at deaktivere TDE for en Azure database, der gemmes dataene, der er overført fra en Stræk aktiverede SQL Server-database skal du gøre følgende ting:

1. Åbn databasen med [Azure-portalen](https://portal.azure.com)
2. Klik på knappen **Indstillinger** i bladet database
3. Vælg indstillingen **gennemsigtig datakryptering**
4. Vælg indstillingen **fra** , og vælg derefter **Gem**




<!--Anchors-->
[Kryptering af gennemsigtig Data (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
