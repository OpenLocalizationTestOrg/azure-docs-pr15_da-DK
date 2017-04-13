<properties
    pageTitle="SQL-Database elastiske puljen pris og ydeevne"
    description="Prisoplysninger specifikke for elastiske database grupper."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Elastiske database puljen fakturerings- og prisoplysninger

Elastiske database grupper er faktureret per følgende egenskaber:

- En elastiske puljen faktureres efter dens oprettelse, selvom der ikke er nogen databaser i puljen.
- En elastiske puljen faktureres hver time. Dette er den samme vha frekvens som for ydeevneniveauer af enkelt databaser.
- Hvis en elastiske puljen tilpasses til en ny mængde eDTUs, derefter faktureres puljen ikke efter det nye antal eDTUS indtil tilpasning af størrelsen på handlingen er udført. Dette følger det samme mønster som ændre enkeltstående databaser præstationsniveau.


- En elastiske puljen pris er baseret på antallet eDTUs af puljen. En elastiske puljen pris er uafhængig af det antal og anvendelsen af elastiske databaser i modellen.
- Kurs beregnes ved hjælp af (antal puljen eDTUs) x (enhedspris per eDTU).

EDTU enhedsprisen for en elastiske puljen er højere end DTU enhedsprisen for en enkeltstående database i det samme niveau i tjenesten. Yderligere oplysninger finder du [priser SQL-Database](https://azure.microsoft.com/pricing/details/sql-database/). 


For at forstå eDTUs og tjenesten lag, skal du se [Indstillinger for SQL-Database og ydeevne](sql-database-service-tiers.md).

## <a name="next-steps"></a>Næste trin

- [Oprette en elastiske database puljen](sql-database-elastic-pool-create-portal.md)
- [Overvåge, administrere og tilpasse størrelsen af en elastiske database puljen](sql-database-elastic-pool-manage-portal.md)
- [Indstillinger for SQL-Database og ydeevne: forstå, hvad der er tilgængeligt i hver tjeneste lag](sql-database-service-tiers.md)
- [PowerShell-script til at identificere databaser, der passer til en elastiske database puljen](sql-database-elastic-pool-database-assessment-powershell.md)
