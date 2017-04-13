<properties
    pageTitle="Oprette forbindelse til SQL-Database ved hjælp af Java med JDBC på Windows | Microsoft Azure"
    description="Viser et eksempel på Java kode du kan bruge til at oprette forbindelse til Azure SQL-Database. Udsnittet bruger JDBC, og den kører på en Windows-klientcomputer."
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Oprette forbindelse til SQL-Database ved hjælp af Java med JDBC i Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Dette emne vises et eksempel på Java-kode, du kan bruge til at oprette forbindelse til Azure SQL-Database. Java-eksempel er baseret på Java Development Kit (JDK) version 1.8. Eksemplet opretter forbindelse til en Azure SQL-Database ved hjælp af JDBC driveren.

## <a name="step-1--configure-development-environment"></a>Trin 1: Konfigurere udviklingsmiljø

[Konfigurere udviklingsmiljø til Java udvikling](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>Trin 2: Oprette en SQL-database

Se på [siden Introduktion](sql-database-get-started.md) til at se, hvordan du opretter en database.  

## <a name="step-3-get-connection-string"></a>Trin 3: Få vist forbindelsesstreng

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Hvis du bruger JTDS JDBC driveren og derefter skal du tilføje "ssl = kræver" til URL-adresse for forbindelsen streng, og du skal angive følgende indstilling for JVM "-Djsse.enableCBCProtection=false". Denne indstilling for JVM deaktiverer en løsning for en sikkerhedsrisiko, så Vær sikker på, at du forstår hvilke risikoen er involveret før indstillingen.

## <a name="step-4-run-sample-code"></a>Trin 4: Køre eksempelkode

* [Bevis for konceptet oprette forbindelse til SQL ved hjælp af Java](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>Næste trin

* Gå til [Java Developer Center](/develop/java/).
* Gennemgå [SQL Database udvikling oversigt](sql-database-develop-overview.md)
* Flere oplysninger om [Microsoft JDBC Driver til SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>Yderligere ressourcer 

* [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Udforske alle [funktionerne i SQL-Database](https://azure.microsoft.com/services/sql-database/)
