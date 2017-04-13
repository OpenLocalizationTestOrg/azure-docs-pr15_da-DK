<properties
   pageTitle="Forretningskontinuitet i skyen - Webdatabase gendannelse - SQL-Database | Microsoft Azure"
   description="Lær, hvordan Azure SQL-Database understøtter skyen Forretningskontinuitet og gendannelse af databasen og hjælper med at holde vigtige skyen programmer, der kører."
   keywords="Forretningskontinuitet skyen Forretningskontinuitet database nedbrud, og gendannelse af databasen"
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
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Oversigt over Forretningskontinuitet med Azure SQL-Database

Denne oversigt beskrives de funktioner, der indeholder Azure SQL-Database til Forretningskontinuitet og nedbrud. Den indeholder indstillinger, anbefalinger og selvstudier til at gendanne fra forstyrrende hændelser, der kan medføre tab af data eller bevirker, at din database og program tilladelse til at være tilgængelige. Diskussionen omfatter hvad du skal gøre, når en bruger eller programfejl påvirker dataintegritet, en Azure område indeholder en afbrydelse eller dit program kræver vedligeholdelse. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL-Database funktioner, som du kan bruge til at levere Forretningskontinuitet

SQL-Database indeholder flere business løbende funktioner, herunder automatiseret sikkerhedskopier og valgfrit databasereplikering. Hver har forskellige egenskaber for anslåede gendannelse klokkeslæt (Indsæt) og muligt tab af data for seneste transaktioner. Når du forstår disse indstillinger, kan du vælge mellem dem - og, i de fleste tilfælde kan bruge dem sammen for forskellige scenarier. Når du udvikler din løbende forretningsplan, skal du forstå den maksimale acceptable tid, før programmet genopretter fuldt efter hændelsen forstyrrende – dette er din gendannelse tid målsætning (RTO). Du skal også forstå den maksimale mængde seneste dataopdateringer (tidsinterval) programmet kan acceptere at miste, når gendanne efter hændelsen forstyrrende - gendannelse punkt formålet (frigivne Produktionsordre). 

Den følgende tabel sammenligner Indsæt og frigivne Produktionsordre for de tre mest almindelige scenarier.

| Egenskab |  Grundlæggende trin | Standard niveau  | Premium niveau |
|---|---|---|---|
| Peg på tid gendanne fra sikkerhedskopi | Gendanne et punkt i 7 dage   | Gendanne et punkt i 35 dage  | Gendanne et punkt i 35 dage |
Geografisk-Gendan fra geografisk replikerede sikkerhedskopier | Indsæt < 12h, frigivne Produktionsordre < 1t   | Indsæt < 12h, frigivne Produktionsordre < 1t   | Indsæt < 12h, frigivne Produktionsordre < 1t |
|Aktive geografisk-gentagelse | Indsæt < 30s, frigivne Produktionsordre < 5s   | Indsæt < 30s, frigivne Produktionsordre < 5s | Indsæt < 30s, frigivne Produktionsordre < 5s |


### <a name="use-database-backups-to-recover-a-database"></a>Bruge databasesikkerhedskopier til at gendanne en database

SQL-Database automatisk udfører en kombination af komplette sikkerhedskopier ugentligt forskelle Webdatabase sikkerhedskopier hver time og transaktionslogfiler hver fem minutter til at beskytte din virksomhed mod datatab. Disse sikkerhedskopier gemmes i lokalt overflødige lagerplads til 35 dage for databaser i Standard- og Premium service niveauer og syv dage for databaser i de grundlæggende webtjenesten – se [service niveauer](sql-database-service-tiers.md) for at få flere oplysninger om tjenesten niveauer. Hvis opbevaringsperiode for din webtjenesten ikke opfylder virksomhedens behov, kan du øge opbevaringsperioden ved at [ændre webtjenesten](sql-database-scale-up.md). Fuld og forskelle databasen sikkerhedskopier replikeres også til en [parvis datacenter](../best-practices-availability-paired-regions.md) til beskyttelse mod en data center afbrydelse - se [automatisk sikkerhedskopiering af database](sql-database-automated-backups.md) for at få flere oplysninger.

Du kan bruge disse automatiske sikkerhedskopier af databasen til at gendanne en database fra forskellige forstyrrende hændelser, både i dit datacenter og til en anden datacenter. Ved hjælp af automatiske sikkerhedskopier af databasen, afhænger det anslåede tidspunkt for gendannelse af flere faktorer, herunder det samlede antal databaser genoprette i samme område på samme tid, databasestørrelse, transaktion logstørrelse og netværksbåndbredde. I de fleste tilfælde er gendannelse tidspunktet mindre end 12 timer. Når gendanne til et andet dataområde, er det muligt tab af data er begrænset til 1 time ved geografisk overflødige opbevaring af hver time forskelle databasesikkerhedskopier. 

> [AZURE.IMPORTANT] Hvis du vil gendanne, ved hjælp af automatiseret sikkerhedskopier, skal du være medlem af rollen bidragyder til SQL Server eller ejeren af abonnementet - se [RBAC: indbyggede roller](../active-directory/role-based-access-built-in-roles.md). Du kan gendanne ved hjælp af portalen Azure, PowerShell eller REST-API. Du kan ikke bruge Transact-SQL.

Brug automatiseret sikkerhedskopier som din business løbende og gendannelsesfiler metode, hvis dit program:

- Ikke betragtes som kommunikations kritiske.
- Har ikke en binding SLA nedetid af 24 timer eller længere ikke resulterer derfor i finansielle ansvar.
- Har en lav rente Dataændring (lav transaktioner i sekundet), og at miste op til en time ændring, som er en acceptabel datatab. 
- Vejer omkostningerne store og små bogstaver. 

Hvis du har brug for hurtigere gendannelse, du brug [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md) (gennemgås næste). Hvis du har brug at kunne genoprette data fra en periode, der er ældre end 35 dage, kan du overveje arkivering databasen jævnligt til en BACPAC fil gemt (en komprimeret fil, der indeholder dine databaseskema og tilknyttede data) i Azure blob-lager eller i et andet sted efter eget valg. Se [oprette en databasekopi](sql-database-copy.md) og [eksportere databasekopi](sql-database-export.md)kan finde flere oplysninger om hvordan du kan oprette et arkiv med en transaktion ensartet database. 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Bruge aktive geografisk-gentagelse til at reducere gendannelse tid og begrænse tab af data er knyttet til en gendannelse

Ud over at bruge databasesikkerhedskopier til gendannelse af databasen i tilfælde af en business forstyrrelse, kan du bruge [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md) til at konfigurere en database for at få op til fire læsbare sekundær databaser i områder efter eget valg. Disse sekundære databaser holdes synkroniseret med den primære database ved hjælp af en asynkron replikering fungerer. Denne funktion bruges til at beskytte mod business afbrydelser i tilfælde af en data center afbrydelse eller under en opgradering af programmet. Aktive geografisk replikering kan også bruges til at levere bedre forespørgsel ydeevne for skrivebeskyttede forespørgsler til geografisk spredt brugerne.

Hvis den primære database går offline uventet, eller du skal tage det offline til vedligeholdelsesaktiviteter, kan du hurtigt hæve et sekundært for at blive den primære (også kaldet en failover) og konfigurerer programmer til at oprette forbindelse til den netop overførte primære. Med en planlagt failover er der ingen tab af data. Med en ikke-planlagt failover, kan der være nogle lidt tab af data for meget seneste transaktioner på grund af art af asynkron gentagelse. Når du har en failover kan du senere failback - efter en plan, eller når datacenteret dukker igen er online. I alle tilfælde skal brugerne opleve en lille mængde nedetid og har brug for at oprette forbindelse igen. 

> [AZURE.IMPORTANT] Hvis du vil bruge aktive geografisk-gentagelse, skal du enten være ejeren af abonnementet eller have administratortilladelser i SQL Server. Du kan konfigurere og belastningsjustering ved hjælp af portalen Azure, PowerShell eller REST-API ved hjælp af tilladelser på dit abonnement eller ved hjælp af Transact-SQL ved hjælp af tilladelser i SQL Server.

Brug aktive geografisk-gentagelse, hvis dit program opfylder følgende kriterier:

- Er kommunikations kritiske.
- Har en serviceaftale (SLA), der ikke tillader af 24 timer eller flere af nedetid.
- Nedetid medfører finansielle ansvar.
- Har en høj sats data ændring er høj og at miste data i timen er ikke acceptable.
- Yderligere omkostninger for aktive geografisk replikering er lavere end de potentielle finansielle ansvar og tilknyttede tab af business.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Gendanne en database efter en bruger eller program fejl

* Ikke er nogen, er perfekt! En bruger kan kommer til at slette nogle data, ved et uheld slippe en vigtige tabel eller endda slippe en hel database. Eller et program kan komme til at overskrive god data med ugyldige data på grund af et program defekt. 

I dette scenario er indstillingerne for gendannelse.

### <a name="perform-a-point-in-time-restore"></a>Udføre en punkt-in-time gendannelse

Du kan bruge de automatiske sikkerhedskopier til at gendanne en kopi af databasen til et kendte gode punkt i gang, forudsat at er inden for opbevaringsperiode database. Når databasen er gendannet, kan du enten den oprindelige database erstattes med den gendannede database eller kopiere de nødvendige data fra de gendannede data til den oprindelige database. Hvis databasen bruger aktive geografisk-gentagelse, anbefaler vi kopiere de nødvendige data fra den gendannede kopi i den oprindelige database. Hvis du erstatter den oprindelige database med den gendannede database, skal du omkonfigurere og synkronisere aktive geografisk-gentagelse (hvilket kan tage lang tid for en stor database). 

Yderligere oplysninger og have en detaljeret vejledning for at gendanne en database til et punkt i gang se ved hjælp af portalen Azure eller ved hjælp af PowerShell, [gendanne punkt i gang](sql-database-recovery-using-backups.md#point-in-time-restore). Du kan ikke gendanne ved hjælp af Transact-SQL.

### <a name="restore-a-deleted-database"></a>Gendanne en slettet database

Hvis databasen er blevet slettet, men logiske serveren ikke er blevet slettet, kan du gendanne den slettede database til det punkt, hvor det er blevet slettet. Dette gendanner en sikkerhedskopi af databasen til den samme logiske SQL server som den er blevet slettet. Du kan gendanne den ved hjælp af det oprindelige navn eller angive et nyt navn eller den gendannede database.

Yderligere oplysninger og have en detaljeret vejledning for at gendanne en slettet database se ved hjælp af portalen Azure eller ved hjælp af PowerShell, [gendanne en slettet database](sql-database-recovery-using-backups.md#deleted-database-restore). Du kan ikke gendannes ved hjælp af Transact-SQL.

> [AZURE.IMPORTANT] Hvis logisk serveren slettes, kan du ikke gendanne en slettet database. 

### <a name="import-from-a-database-archive"></a>Importere fra en database arkiv

Hvis datatab sted uden for den aktuelle opbevaringsperiode for automatisk sikkerhedskopiering, og du har arkivering databasen kan du [importere en arkiveret BACPAC-fil](sql-database-import.md) til en ny database. På dette tidspunkt du enten den oprindelige database erstattes med den importerede database eller kopiere de nødvendige data fra de importerede data til den oprindelige database. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Gendanne en database til et andet område fra en Azure regionale data center afbrydelse

<!-- Explain this scenario -->

Selvom sjældne, kan en Azure datacenter har en afbrydelse. Når der opstår en afbrydelse, medfører en business forstyrrelse, der kan kun sidste et par minutter eller kan sidst i timer. 

- En af mulighederne er at vente databasen til at komme igen er online, når data center afbrydelse er. Det fungerer for programmer, der har råd til at databasen er offline. Et development projekt eller en gratis prøveversion kan du f.eks, ikke behøver at arbejde på konstant. Når et datacenter har en afbrydelse, kender ikke du hvor længe afbrydelse holder, så denne indstilling virker kun, hvis du ikke har brug for din database til et stykke tid.
- En anden indstilling er Sådan enten flytter til et andet dataområde, hvis du bruger aktive geografisk replikering eller Gendan ved hjælp af geografisk overflødige databasesikkerhedskopier (geografisk-Gendan). Failover tager kun et par sekunder, mens gendannelse fra sikkerhedskopier tager timer.

Når du udfører handlinger, hvor lang tid det tager dig til at gendanne, og hvor meget der påløber i tilfælde af en data center afbrydelse tab af data, afhænger af, hvordan du beslutter dig for at bruge de business løbende funktioner, der er beskrevet ovenfor i dit program. Faktisk, kan du vælge at bruge en kombination af databasesikkerhedskopier og aktive geografisk-gentagelse afhængigt af dine krav til programmet. Finde [Design et program til skyen nedbrud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) og [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)på en beskrivelse af programmet Designovervejelser separat databaser og elastiske grupper ved hjælp af funktionerne business løbende.

I nedenstående afsnit indeholder en oversigt over disse trin for at gendanne ved hjælp af databasesikkerhedskopier eller aktive geografisk-gentagelse. Detaljeret vejledning, herunder planlægning af krav, indlæg gendannelse trin og oplysninger om, hvordan du simulere en afbrydelse for at udføre en nedbrud gendannelse analysér, skal du se [gendanne en SQL-Database fra en afbrydelse](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Forberede en afbrydelse

Uanset løbende business funktion du bruger, skal du:

- Identificere og forberede den destinationsadresse-server, herunder serverniveau firewallregler, logon og master databasen niveau tilladelser.
- Finde ud af, hvordan man omdirigerer klienter og klientprogrammer til den nye server
- Dokument andre afhængigheder, som overvågning indstillinger og beskeder 
 
Hvis du ikke planlægge og forberede korrekt, hvilket programmerne online efter en failover eller en genoprettelse tager yderligere tid og dermed også Kræv fejlfinding på et tidspunkt for belastning - en forkert kombination.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Failover til en sekundær geografisk replikerede database 

Hvis du bruger aktive geografisk replikering som din metode til gendannelse, [gennemtvinge en failover til en geografisk replikerede sekundær](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Inden for sekunder, sekundært er fremhævede for at blive den nye primære og er klar til at registrere nye poster og besvare alle forespørgsler - med kun et par sekunder om tab af data for de data, der ikke havde endnu er blevet replikeres. Du kan finde oplysninger om automatisk failover processen, [Design et program til skyen nedbrud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Når datacenteret kommer igen er online, kan du failback til den oprindelige primære (eller ej).

### <a name="perform-a-geo-restore"></a>Udføre en geografisk-Gendan 

Hvis du bruger til automatisk sikkerhedskopiering med geografisk overflødige lagerplads gentagelse som din metode til gendannelse, [starte et databaser gendannelse ved hjælp af geografisk-Gendan](sql-database-disaster-recovery.md#recover-using-geo-restore). Gendannelse normalt finder sted senest 12 timer - med tab af data på op til en time afhænger af, hvornår den seneste hver time sikkerhedskopi med truffet og replikerede. Indtil genoprettelsen er fuldført, kan databasen ikke til at registrere en hvilken som helst transaktioner eller besvare alle forespørgsler. 

> [AZURE.NOTE] Hvis datacenteret kommer igen er online, før du skifter dit program over til gendannet databasen, kan du blot annullere at genoprettelsen.  

### <a name="perform-post-failover--recovery-tasks"></a>Udføre indlæg failover / gendannelse opgaver 

Efter genoprettelse fra enten gendannelse ordning, skal du udføre følgende yderligere opgaver før dine brugere og programmer er tilbage, op at køre:

- Omdiriger klienter og klientprogrammer til den nye server og gendannede database
- Sikre relevant serverniveau firewallregler er på plads for brugerne at oprette forbindelse (eller brug [database niveau firewalls](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Sikre relevant logon og tilladelser på brugerniveau master databasen er på plads (eller brug [indeholdt brugere](https://msdn.microsoft.com/library/ff929188.aspx))
- Konfigurere overvågning, efter behov
- Konfigurere beskeder, efter behov

## <a name="upgrade-an-application-with-minimal-downtime"></a>Opgradere et program med minimale nedetid

Nogle gange skal et program gøres tilgængelige offline på grund af planlagt vedligeholdelse som en opgradering af programmet. [Administrer programmet opgraderinger](sql-database-manage-application-rolling-upgrade.md) beskriver, hvordan du bruger aktive geografisk-replikering til at aktivere rullende opgraderinger af skyen programmet for at minimere nedetid under opgraderinger og angive en gendannelse sti, i tilfælde af, at noget går galt. I denne artikel kigger på to forskellige metoder til orchestrating opgraderingsprocessen og diskuterer fordele og kompromiser for hver indstilling.

## <a name="next-steps"></a>Næste trin

Finde [Design et program til skyen nedbrud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) og [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)på en beskrivelse af programmet Designovervejelser separat databaser og elastiske grupper.






