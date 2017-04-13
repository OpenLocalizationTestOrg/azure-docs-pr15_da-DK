<properties 
   pageTitle="Azure SQL-Database ofte stillede spørgsmål" 
   description="Svar på almindelige spørgsmål kunder Bed om skyen databaser og Azure SQL-Database, Microsofts relationsdatabase administrationssystem (RDBMS) og database som en tjeneste i skyen." 
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
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>SQL-Database ofte stillede spørgsmål

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Hvordan brugen af SQL-Database vises på din faktura? 
SQL-Database fakturaer på en mere forudsigelige hver time sats baseret på begge service niveau + ydeevneniveau for for enkelt databaser eller eDTUs per elastiske database puljen. Faktiske forbrug beregnes og forholdsmæssig hver time, så din faktura viser muligvis brøkdele af en time. Hvis der findes en database til 12 timer i løbet af en måned, vises din faktura eksempelvis brugen af 0,5 dage. Desuden er service niveauer + ydeevneniveau og eDTUs hver gruppe opdelt på den faktura, at gøre det nemmere at se antallet af database dage, du har brugt for hvert navn i en enkelt måned.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Hvad nu, hvis en enkelt database er aktiv for mindre end en time eller anvender en højere webtjenesten mindre end en time?
Du er faktureret for hver time, der findes en database ved hjælp af den højeste webtjenesten + ydeevneniveau, der anvendes i forbindelse med denne time, uanset brugen eller om databasen var aktiv for mindre end en time. Hvis du opretter en enkelt database og slette den fem minutter senere afspejler din faktura for eksempel et gebyr for én database time. 

Eksempler
    
- Hvis du opretter en grundlæggende database og derefter opgraderer det til Standard S1 med det samme, betaler du med Standard S1 hastighed for den første time.

- Hvis du opgraderer en database fra grundlæggende til Premium på kl. og opgraderingen er fuldført på 1:35 AM den følgende dag betaler du med Premium hastighed starte klokken 1:00 

- Hvis du nedgradere en database fra Premium til grundlæggende klokken 11:00 og det afsluttes kl. 2:15, og derefter databasen som registreres til Premium rente indtil 3:00 PM, hvorefter den som registreres til de grundlæggende satser.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Hvordan elastiske database puljen brugen vises på din faktura og hvad der sker, når feltstørrelsen ændres eDTUs per puljen?
Elastiske database puljen gebyrer vises på din faktura som elastiske DTUs (eDTUs) i de intervaller, der vises under eDTUs per puljen på [siden priser](https://azure.microsoft.com/pricing/details/sql-database/). Der er gratis-database til elastiske database grupper. Du er faktureret for hver time, der er en samling findes på den højeste eDTU, uanset brugen eller om puljen var aktiv for mindre end en time. 

Eksempler

- Hvis du opretter en Standard elastiske database pulje med 200 eDTUs på 11:18 AM, betaler tilføje fem databaser til puljen, du for 200 eDTUs for hele timetallet, startende fra 11 AM gennem resten af dagen.
- I dag 2, på 5:05 AM, Database 1 begynder forbrug 50 eDTUs og indeholder stabil gennem dagen. Databaser 2-5 varierer mellem 0 og 80 eDTUs. Under dag, kan du føje fem andre databaser, der forbruger variabel eDTUs i løbet af arbejdsdagen. Dag 2 er en fuld dag faktureret på 200 eDTU. 
- På dag 3, på 5 AM Du kan tilføje en anden 15 databaser. Database brugen øges i løbet af arbejdsdagen til det punkt, hvor du beslutter dig for at øge eDTUs til puljen fra 200 til 400 kl. 8:05 Gebyrer på niveauet for 200 eDTU var gældende, indtil 8 pm og øger til 400 eDTUs for de sidste fire timer. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>Hvordan brugen af aktive geografisk-gentagelse i en elastiske database puljen vises på din faktura?
I modsætning til enkelt databaser har ved hjælp af [Aktive geografisk replikering](sql-database-geo-replication-overview.md) med elastiske databaser ikke en direkte indvirkning fakturering.  Du betaler kun for eDTUs klargjort for hver af grupper (puljen primære og sekundære puljen)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Hvordan påvirker brugen af overvågningsfunktionerne din faktura? 
Overvågning er indbygget i tjenesten SQL-Database uden ekstra omkostninger og er tilgængelige for Basic, som Standard og Premium databaser. Men for at gemme overvågningslogge, de overvåge funktion anvendelser kontotypen Azure-lager og satser for tabeller og køer i Azure-lager, gælder baseret på størrelsen af din overvågningsloggen.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>Hvordan finder jeg det rette service niveau og ydeevne niveau for enkelt databaser og elastiske database grupper? 
Nogle funktioner er tilgængelige for dig. 

- Bruge [DTU størrelseshåndtag advisor](http://dtucalculator.azurewebsites.net/) anbefale databaser og DTUs, der kræves for lokale databaser, og Evaluer flere databaser for elastiske database grupper.
- Hvis en enkelt database kan fordel der i en gruppe, anbefaler Azure's intelligent program en elastiske database puljen, hvis det registrerer en tidligere forbrug mønster, der bør føre til den. Se [skærm og administrere en elastiske database puljen ved hjælp af Azure portal](sql-database-elastic-pool-manage-portal.md). Få mere at vide om, hvordan du selv foretage beregningerne, skal du se [pris og ydeevne overvejelser i forbindelse med en elastiske database puljen](sql-database-elastic-pool-guidance.md)
- For at se, om du skal bruge til at ringe til en enkelt database op eller ned, skal du se [ydeevne vejledningen til enkelt databaser](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Hvor ofte kan jeg ændre service niveau eller ydeevne niveauet for en enkelt database? 
Med version 12 databaser, kan du ændre webtjenesten (mellem Basic, som Standard og Premium) eller niveauet ydeevne i et webtjenesten (for eksempel, S1 til S2) så mange gange du vil. For databaser fra tidligere versioner, kan du ændre niveauet for tjenesten niveau eller ydeevne op til fire gange i en 24-timers periode.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Hvor ofte kan jeg justere eDTUs per puljen? 
Så tit du ønsker.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>Hvor lang tid tager det at ændre service niveau eller ydeevne niveauet for en enkelt database eller flytte en database og en elastiske database puljen? 
Ændre serviceniveauet i en database og flytte ind og ud af en samling kræver, at databasen skal kopieres på platformen som en Baggrundshandling. Ændre webtjenesten kan udføre fra et par minutter til flere timer afhængigt af størrelsen på databaserne. I begge tilfælde forbliver databaserne online og tilgængelig under flytningen. Få mere at vide om ændring af enkelt databaser, skal du se [ændre tjenesten niveau i en database](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Hvornår skal jeg bruge en enkelt database kontra elastiske databaser? 
Generelt elastiske database grupper er udviklet til et typisk [software som en tjeneste (SaaS) programmet mønster](sql-database-design-patterns-multi-tenancy-saas-applications.md), hvor der ikke er én database hver kunde eller lejer. Købe individuelle databaser og overprovisioning for at opfylde variablen og hastigheder under spidsbelastning behov for hver database er ofte ikke omkostninger effektivt. Med grupper, du administrere samlede performance for puljen, og databaserne skalere automatisk op og ned. 

Azure's intelligent program anbefaler en samling for databaser, når et mønster for brug bør føre til den. Yderligere oplysninger finder du i [SQL-Database priser niveau anbefalinger](sql-database-service-tier-advisor.md). Du kan finde detaljerede vejledning om at vælge mellem enkelt og elastiske databaser, [pris og ydeevne overvejelser i forbindelse med elastiske database grupper](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Hvad betyder det har op til 200% af den maksimale klargjorte database lagerplads til lagring af sikkerhedskopier? 
Sikkerhedskopieringen er den lagerplads, der er knyttet til databasesikkerhedskopier automatiseret, der bruges til [punkt-i-tid-Gendan](sql-database-recovery-using-backups.md#-point-in-time-restore) og [Geografisk-Gendan](sql-database-recovery-using-backups.md#geo-restore). Microsoft Azure SQL Database indeholder op til 200% af den maksimale klargjorte database lagerplads ekstra lagerplads gratis. Hvis du har en forekomst af Standard DB med en klargjort DB størrelse på 250 GB, kan du f.eks, der følger med 500 GB ekstra lagerplads gratis. Hvis databasen overstiger den medfølgende ekstra lagerplads, kan du vælge at reducere opbevaringsperioden ved at kontakte Azure Support eller købe ekstra sikkerhedskopiering opbevaring faktureret til læseadgang geografisk overflødige lagerplads (RA-GRS) standardsats. Se lagerplads priser oplysninger kan finde flere oplysninger om RA-GRS fakturering.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Jeg er flyttes fra Web/Business til de nye tjeneste lag, hvad skal jeg ellers vide?
Azure SQL Web og Business databaser er nu går på pension. De grundlæggende, Standard, Premium og elastiske lag Erstat retiring Web og Business databaserne. Vi har flere ofte stillede spørgsmål, der kan hjælpe dig i denne overgangsperiode. [Internettet og Business Edition sunset ofte stillede spørgsmål](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Hvad er en forventede gentagelse mellemliggende ved geografisk replikering en database mellem to områder i den samme Azure Geografi?  
Vi understøtter i øjeblikket en frigivne Produktionsordre af fem sekunder og gentagelse mellemliggende er mindre end, når geografisk-sekundært er hostet i Azure anbefalede parvis område og på samme niveau i tjenesten.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Hvad er en forventede gentagelse mellemliggende, når der oprettes geografisk sekundær i det samme område som den primære database?  
Baseret på empiriske data, er der ikke for meget forskellen mellem handel region og mellemliggende tid mellem område gentagelse når Azure anbefalede parvis region bruges. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Hvis der er et netværk mislykkedes mellem to områder, hvordan forsøg fungerer når geografisk replikering er konfigureret?  
Hvis der er en Afbryd forbindelse, kan vi gentagne hvert 10 for at genoprette forbindelser.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Hvad kan jeg gøre for at sikre, at en kritiske ændring på den primære database replikeres?
Geografisk-sekundær er en asynkron kopi, og vi forsøg ikke at beholde den i fuld synkronisering med primært. Men vi giver dig en metode til at gennemtvinge synkronisering for at sikre, at replikering af vigtige ændringer (for eksempel adgangskode opdateringer). Tvunget synkronisering påvirker ydeevnen, fordi den blokerer opkald tråd, indtil alle bekræftede transaktioner replikeres. Yderligere oplysninger finder du [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Hvilke funktioner er tilgængelige til at overvåge gentagelse mellemliggende tid mellem den primære database og geografisk sekundær?
Vi fremvise realtid gentagelse mellemliggende tid mellem den primære database og geografisk-sekundær gennem en DMV. Yderligere oplysninger finder du [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
