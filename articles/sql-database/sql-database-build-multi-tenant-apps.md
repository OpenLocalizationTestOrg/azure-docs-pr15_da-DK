<properties
   pageTitle="Azure SQL-Database opbygger med flere lejer Apps med isolationsniveauet og effektivitet"
   description="Få mere at vide, hvordan opbygger SQL-Database med flere lejer apps"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Opbygger med flere lejer Apps med Azure SQL-Database med isolationsniveauet og effektivitet

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Udnytte elastiske grupper og opbygge mere effektivt med flere lejer apps

Hvis du er en SaaS Udviklingscenter skriver en med flere lejer app og håndtering af mange kunder, foretager du ofte kompromiserne i kunde ydeevne, administration og sikkerhed. Med Azure SQL Database elastiske Database grupper har du ikke længere at gøre sammensat. Disse grupper hjælpe dig med at administrere og overvåge med flere lejer apps og få isolationsniveauet fordelene ved en kunde i databasen. Se [designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![build-multi-lejer-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>Automatisk skalering du styre

Grupper skalere automatisk ydeevne og lagerkapacitet til elastiske databaser på farten. Du kan styre den ydeevne, der er tildelt til en gruppe, tilføje eller fjerne elastiske databaser efter behov og definere ydeevnen af elastiske databaser uden at påvirke de samlede omkostninger for puljen. Det betyder, at du ikke behøver at bekymre dig om administration af brugen af individuelle databaser.

[Læs dokumentationen](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Intelligent administration af dit miljø

Indbyggede størrelseshåndtag anbefalinger identificere proaktiv databaser, der ville få glæde af grupper. Disse anbefalinger Tillad "what if-" analyse til hurtig optimering til at opfylde dine mål. RTF-ydeevne overvågning og fejlfinding dashboards hjælpe dig med at visualisere historiske puljen anvendelsen.

[Læs dokumentationen](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Ydeevne og pris til dine behov

Grundlæggende, Standard, og Premium grupper giver dig en bred spektret af ydeevne, opbevaring og priser indstillinger. Grupper kan indeholde op til 400 elastiske databaser. Elastiske databaser kan automatisk skalering op til 1000 elastiske database transaktion enheder (eDTU).

[Læs dokumentationen](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Elastiske værktøjer

Ud over elastiske grupper er der SQL-Database funktioner til at administrere funktionsdygtige aktiviteter på tværs af flere databaser:

**Udføre tværs databaseforespørgsler og -rapportering.**  
[Elastiske databaseforespørgsel](sql-database-elastic-query-overview.md) gør det muligt at køre forespørgsler eller rapporter på tværs af databaser i din elastiske puljen og få adgang til eksterne data, der er gemt i mange databaser i din gruppe, på én gang.

**Køre cross databasetransaktioner.**  
[Elastiske databasetransaktioner](sql-database-elastic-transactions-overview.md) gør det muligt at køre transaktioner, der strækker sig over flere databaser i SQL-databaser og udføre handlinger (dvs. Når behandling af finansielle transaktioner på tværs af databaser, eller når du opdaterer lager i en database og ordrer).

**Udføre de samme handlinger på flere databaser.**  
[Elastiske database job](sql-database-elastic-jobs-overview.md) udføre administrative handlinger som genopbygge indeks eller opdatere skemaer på tværs af hver database i din elastiske puljen.

Gå til startsiden for at se, hvad andre SQL-Database har til at tilbyde.
[Tjekke filen ud](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Næste trin

Få en [gratis Azure abonnement](https://azure.microsoft.com/get-started/) og [oprette din første Azure SQL-Database](sql-database-get-started.md).

## <a name="additional-resources"></a>Yderligere ressourcer

Udforsk alle [funktionerne i SQL-Database](https://azure.microsoft.com/services/sql-database/).
 
Gennemgå [Teknisk oversigt over SQL-Database](sql-database-technical-overview.md).  
