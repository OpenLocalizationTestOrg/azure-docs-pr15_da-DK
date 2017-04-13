<properties
    pageTitle="Hvad er SQL-Database? Introduktion til SQL-Database | Microsoft Azure"
    description="Få en introduktion til SQL-Database: tekniske detaljer og funktionerne i Microsoft-relationel database administrationssystem (RDBMS) i skyen."
    keywords="Introduktion til sql, Introduktion til sql, hvad er sql-database"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>Hvad er SQL-Database? Introduktion til SQL-Database

SQL-Database er en relationsdatabase tjeneste i skyen baseret på markedet linjeafstand Microsoft SQL Server program med vigtige funktioner. SQL-Database leverer mere forudsigelige ydeevne, skalerbarhed uden nedetid, Forretningskontinuitet og databeskyttelse – alt sammen i nærheden af nul administration. Du kan fokusere på hurtig app udvikling og fremskynde din tid på markedet i stedet for administration af virtuelle maskiner og infrastruktur. Da den er baseret på [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) -programmet, understøtter SQL-Database eksisterende SQL Server-værktøjer, biblioteker og API'er, hvilket gør det nemmere for dig at flytte og udvide til skyen.

I denne artikel er en introduktion til SQL-Database grundlæggende begreber og funktioner, der er relateret til ydeevne, skalerbarhed og administration, med links til at udforske detaljer. Hvis du er klar til at hoppe i, kan du [oprette din første SQL-database](sql-database-get-started.md) eller [oprette en elastiske database puljen](sql-database-elastic-pool-create-portal.md) i minutter. Hvis du vil en dybere undersøgelse, skal du se denne video, 30 minutter.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Justere ydeevnen og skalering uden nedetid

SQL-databaser er tilgængelig i Basic, som Standard og Premium *service niveauer*. Hver webtjenesten indeholder [forskellige niveauer af ydeevne og funktioner](sql-database-service-tiers.md) til at understøtte lightweight til kraftigt database arbejdsbelastninger. Du kan oprette din første app på en lille database til et par bucks en måned, derefter [Skift webtjenesten](sql-database-scale-up.md) manuelt eller automatisk til enhver tid som din app går virus på verdensplan uden nedetid for din app eller dine kunder.

For mange virksomheder og apps er at kunne oprette databaser og Ring op enkelt databasens ydeevne op eller ned efter behov nok, især hvis brugsmønstre er relativt forudsigelige. Men hvis du har uventede brugsmønstre, det kan gøre det svært at administrere omkostninger og business modellen.

[Elastiske grupper](sql-database-elastic-pool.md) i SQL-Database løses dette problem. Som det er nemt. Du allokerer ydeevne til en gruppe og betale for samlede ydeevnen for puljen i stedet for enkelt databasens ydeevne. Du behøver ikke at ringe databasens ydeevne, op eller ned. Databaser i puljen, kaldet *elastiske databaser*, skalere automatisk op og ned til opfylder behov. Elastiske databaser forbruge, men overstiger ikke grænserne for gruppen, så dine omkostninger forbliver forudsigelige, selvom databasen brugen ikke. Desuden kan du [tilføje og fjerne databaser til puljen](sql-database-elastic-pool-manage-portal.md), skalering din app fra en håndfuld databaser til tusindvis, alle budgettet, som du styrer. Hvis du vil vide mere om designs til SaaS programmer, der bruger elastiske grupper skal du se [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Uanset hvad du går – enkelt eller elastiske – du ikke er låst. Du kan blande enkelt databaser med elastiske database grupper og ændre tjenesten niveauer af enkelt databaser og grupper til at oprette innovative design. Desuden med funktioner og rækkevidde Azure kan du mix og match Azure services med SQL-Database til dine behov entydige moderne app design, drive omkostninger og effektivitet og låse op for nye muligheder for business.

Men hvordan kan du sammenligne relative ydeevnen for databaser og database grupper? Hvordan ved du indstillingen Højreklik på stop, når du ringer op og ned? Svaret er Database transaktion enhed (DTU) for enkelt databaser og elastiske DTU (eDTU) for elastiske databaser og database grupper. Se [Indstillinger for SQL-Database og ydeevne: forstå, hvad der er tilgængeligt i hver webtjenesten](sql-database-service-tiers.md) få mere at vide.

## <a name="keep-your-app-and-business-running"></a>Holde din app og virksomheden kørende

Azures branche foranstillet 99,99% tilgængelighed serviceniveauaftale [(SLA)](http://azure.microsoft.com/support/legal/sla/), drevet af et globalt netværk med Microsoft-administreret datacentre for at beskytte din app kører 24/7. Med hver SQL-database, kan du drage fordel af indbyggede databeskyttelse, fejltolerance og beskyttelse af data, som du ellers kan have til at designe, købe, oprette og administrere. Du kan endda så kræve flere lag af beskyttelse for at sikre, at din app og din virksomhed kan gendanne hurtigt i tilfælde af nedbrud, en fejl eller noget andet afhængigt af kravene for din virksomhed. SQL-Database stiller hver webtjenesten med en anden menu med funktioner, du kan bruge til at komme i gang og kører, og hold på denne måde. Du kan bruge punkt-in-time Gendan til at returnere en database til en tidligere tilstand helt 35 dage. Hvis det datacenter, der er vært for dine databaser oplever en afbrydelse, kan du desuden failover til replikaer i et andet område. Eller du kan bruge replikaer til opgraderinger eller flytning til forskellige områder.

![SQL-Database geografisk-gentagelse](./media/sql-database-technical-overview/azure_sqldb_map.png)


Du kan finde oplysninger om de forskellige business løbende-funktioner, der er tilgængelige for forskellige niveauer i [Forretningskontinuitet](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Sikre dine data
SQL Server har en tradition for dækkende datasikkerhed, SQL-Database henhold med funktioner, der begrænser adgang, beskytte data og hjælpe dig med at overvåge aktivitet. Se [sikring af SQL-database](sql-database-security.md) til en hurtig oversigt over sikkerhedsindstillinger, du har i SQL-Database. Se [Security Center til SQL Server Database Engine og SQL-Database](https://msdn.microsoft.com/library/bb510589) til en mere omfattende visning af funktioner for sikkerhed. Og gå til [Azure Sikkerhedscenter](https://azure.microsoft.com/support/trust-center/security/) oplysninger om Azure's platform sikkerhed.

## <a name="next-steps"></a>Næste trin
Nu, hvor du har læst en introduktion til SQL-Database og svar på spørgsmålet "Hvad er SQL-Database?", er du klar til:

- Se [priser side](https://azure.microsoft.com/pricing/details/sql-database/) til enkelt database og elastiske database omkostninger sammenligninger og beregningsprogrammer.
- Få mere at vide om [elastiske grupper](sql-database-elastic-pool.md).
- Kom i gang ved at [oprette din første database](sql-database-get-started.md).
- [Oprette forbindelse og forespørgsler med SSMS](sql-database-connect-query-ssms.md)
- Oprette din første app i C#, Java, Node.js, PHP, Python eller fonetisk: [dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md)
- Få vist et indeks over titler og beskrivelser af [alle emner til Azure sql-database-tjenesten](sql-database-index-all-articles.md).
