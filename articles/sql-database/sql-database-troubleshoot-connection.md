<properties
    pageTitle="Databasen på serveren er ikke tilgængelig i øjeblikket, oprette forbindelse til SQL-Database | Microsoft Azure"
    description="Foretage fejlfinding af databasen på serveren er ikke tilgængelig i øjeblikket fejl, når et program, der opretter forbindelse til SQL-Database."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="databasen på serveren er ikke tilgængelig i øjeblikket, oprette forbindelse til sql-database"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Fejlen "Databasen på serveren er ikke tilgængelig i øjeblikket" Når du opretter forbindelse til sql-database
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Når et program opretter forbindelse til en Azure SQL-database, modtager du følgende fejlmeddelelse:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Denne fejlmeddelelse er typisk midlertidig (forbigående).

Denne fejl opstår, når Azure databasen er ved at blive flyttet (eller har konfigureret) og dit program mister forbindelsen til SQL-database. SQL-database konfigureres igen hændelser opstår på grund af en planlagt begivenhed (for eksempel en Softwareopgradering) eller en ikke-planlagt begivenhed (for eksempel en processen går ned eller belastning). De fleste konfigureres igen hændelser er normalt forbigående og skal udføres i mindre end 60 sekunder højst. Dog kan disse hændelser undertiden tage længere tid at afslutte, som når en stor transaktion medfører en længerevarende genoprettelse.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Trin til at løse midlertidige forbindelsesproblemer
1.  Se [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) for afbrydelser af kendte, der er indtruffet på det tidspunkt, hvor fejlene er rapporteret af programmet.
2. Programmer, der har forbindelse til en skybaseret tjeneste, som Azure SQL-Database skal forventer at være periodiske konfigureres igen begivenheder og implementere retry-logik for at håndtere disse fejl i stedet for fremhævelse disse som programfejl til brugere. Gennemgå sektionen [midlertidige fejl](sql-database-connectivity-issues.md) og de bedste fremgangsmåder og design retningslinjer på [SQL Database udvikling oversigt](sql-database-develop-overview.md) kan finde flere oplysninger og generelle prøv igen strategier. Derefter skal du se kodeeksempler på [Dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md) til specifikke oplysninger.
3.  Når en database nærmer sig dens ressource begrænsninger, kan den virke skal være et problem med midlertidige netværksforbindelsen. Se [fejlfinding af problemer med ydeevnen](sql-database-troubleshoot-performance.md).
4.  Hvis forbindelsesproblemer fortsætter, eller hvis varigheden, dit program støder på fejlen overstiger 60 sekunder eller hvis du får vist flere forekomster af fejlen i en given dag, kan du sende en Azure supportanmodning ved at vælge **Få Support** på webstedet [Azure understøtter](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Næste trin
- Hvis du modtager en anden fejl, kan du evaluere [fejlmeddelelse](sql-database-develop-error-messages.md) for oplysninger om årsagen.
- Hvis problemet bliver fast, kan du besøge vejledning i [fejlfinding af almindelige problemer med mailforbindelsen til Azure SQL-Database](sql-database-troubleshoot-common-connection-issues.md).
