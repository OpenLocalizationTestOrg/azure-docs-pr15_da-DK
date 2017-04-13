<properties
    pageTitle="SQL (PaaS) kontra SQL-databaseserver i skyen på FOS (IaaS) | Microsoft Azure"
    description="Få mere at vide, hvilke skyen SQL Server option passer til dit program: Azure SQL (PaaS)-Database eller SQL Server i skyen på virtuelle Azure-computere."
    services="sql-database, virtual-machines"
    keywords="SQL Server skyen, SQL Server i skyen, PaaS database i skyen SQL Server, DBaaS"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Vælg en skybaseret SQL Server option: Azure SQL (PaaS)-Database eller SQL Server på Azure FOS (IaaS)

Azure har to muligheder for vært for SQL Server arbejdsbelastninger i Microsoft Azure:

* [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/): A SQL-database, der er indbygget i skyen, også kaldet en platform som en (PaaS) tjenestedatabase eller en database som en tjeneste (DBaaS), der er optimeret til software som en tjeneste (SaaS) app udvikling. Det er kompatibel med de fleste SQL Server-funktioner. Se [Hvad er PaaS](https://azure.microsoft.com/overview/what-is-paas/)yderligere oplysninger om PaaS.
* [SQL Server på virtuelle Azure-computere](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installeret og hostet i skyen på Windows Server virtuelle maskiner (VM'er) kører på Azure, også kaldet en infrastructure som en tjeneste (IaaS).
SQL Server på Azure virtuelle maskiner er optimeret til overføre eksisterende SQL Server-programmer. Alle versioner og udgaver af SQL Server er tilgængelige. Det giver adgang til 100% kompatibilitet med SQL Server, så du kan hoste så mange databaser, som er nødvendig og udføre kryds-databasetransaktioner. Det giver adgang til fuld kontrol i SQL Server og Windows.

Få mere at vide, hvordan hver indstilling passer ind i Microsoft dataplatform og få hjælp, der svarer til den rigtige indstilling til virksomhedens behov. Om du prioritere penge eller minimale administration forud for alt andet, kan denne artikel hjælpe dig med at beslutte, hvordan du håndterer leverer mod forretningsbehov, der interesserer dig mest.


## <a name="microsofts-data-platform"></a>Microsofts dataplatform

En af de første ting at forstå i en diskussion i Azure kontra lokal SQL Server-databaser er, at du kan bruge det hele. Microsofts dataplatform bruger SQL Server-teknologi og gør det tilgængeligt på tværs af fysisk lokalt maskiner, private skyen miljøer, fra tredjepart hostet privat skyen miljøer og offentlige skyen. SQL Server på virtuelle Azure mchines gør det muligt at forretningsbehov entydigt og forskelligartet via en kombination af i det lokale miljø og skyen hostet installationer, når du bruger det samme sæt serverprodukter, udviklingsværktøjer og viden på tværs af disse miljøer.

   ![SQL Server-indstillinger i skyen: SQL server på IaaS eller SaaS SQL-database i skyen.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som det ses i diagrammet, hvert tilbud kan være er betegnet ved niveauet i administration, du har over infrastruktur (på X-aksen) og graden af omkostningseffektivitet opfyldes af database niveau konsolideringen og automatisering (på Y-aksen).

Når du udformer et program, er fire grundlæggende indstillinger er tilgængelige som vært for SQL Server en del af programmet:

- SQL Server på ikke-virtualiseret fysiske computere
- SQL Server på virtualiseret lokalt computere (privat skyen)
- SQL Server i Azure Virtual Machine (Microsoft offentlige skyen)
- Azure SQL-Database (Microsoft offentlige skyen)

I følgende afsnit skal du lære om SQL Server på Microsoft offentlige skyen: Azure SQL-Database og SQL Server på Azure FOS. Desuden kan udforske du almindelige business motivators for at bestemme, hvilken indstilling der passer bedst til dit program.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Et nærmere Kig på Azure SQL-Database og SQL Server på Azure FOS

**Azure SQL-Database** er en relationel database-som-en-tjeneste (DBaaS) hostet i Azure skyen, der falder i de forskellige kategorier branche af *Software som-en-tjenesten (SaaS)* og *Platform som-en-tjenesten (PaaS)*. [SQL-database](sql-database-technical-overview.md) er baseret på standardiseret hardware og software, der er ejet, hostet og vedligeholdes af Microsoft. Med SQL-Database, kan du udvikle direkte på tjenesten ved hjælp af indbyggede funktioner og funktionalitet. Når du bruger SQL-Database, du overkommelige med mulighed for at skalere op eller ud for større power uden afbrydelser.

**SQL Server på virtuelle Azure-computere (FOS)** falder i kategorien branche *Infrastruktur som-en-tjenesten (IaaS)* , og gør det muligt at køre SQL Server i en virtuel maskine i skyen. Svarer til SQL-Database, den er bygget på standardiseret hardware, der er ejet hosted og vedligeholdes af Microsoft. Når du bruger SQL Server på en VM, kan du enten løn – så du-gå til en SQL Server-licens, der allerede er medtaget i et billede af SQL Server eller nemt at bruge en eksisterende licens. Du kan også nemt skala-op/ned og pause/CV VM efter behov.

Generelt er disse to SQL-indstillinger optimeret til forskellige formål:

- **SQL-Database** er optimeret til at reducere de samlede udgifter til minimum til klargøring og administration af mange databaser. Det reducerer administrationsomkostninger til løbende, fordi du ikke behøver at administrere en hvilken som helst virtuelle maskiner, operativsystem eller database-software. Du behøver ikke at administrere opgraderinger, høj tilgængelighed eller [Sikkerhedskopier](sql-database-automated-backups.md). Generelt Azure SQL-Database kan øge antallet af databaser, der administreres af en enkelt IT eller udvikling ressource.
- **SQL Server kører på Azure FOS** er udviklet til at overføre eksisterende programmer til Azure eller udvide eksisterende lokale programmer i skyen i hybridinstallationer. Desuden kan du bruge SQL Server i en virtuel maskine til at udvikle og teste traditionelle SQL Server-programmer. Med SQL Server på Azure FOS har du de fulde administratorrettigheder over en dedikeret SQL Server-forekomst, og en skybaseret VM. Det er en perfekt valg, når en organisation allerede har IT-ressourcer, der er tilgængelige til at vedligeholde virtuelle computere. Disse funktioner gør det muligt at opbygge en meget tilpassede system programmets specifikke ydeevnen og af tilgængelighedskrav.

Den følgende tabel opsummerer de vigtigste karakteristika for SQL-Database og SQL Server på Azure FOS:

|       | SQL-Database | SQL Server på en Azure virtuelt|
| -------------- | ------------ | ---------------------- |
| **Bedst til:** | Nye skyen designede programmer, som har tidsbegrænsninger i udvikling og marketing. |Eksisterende programmer, der kræver hurtig overførsel til skyen med minimale ændringer. Hurtig udvikling og test scenarier, når du ikke vil købe lokale ikke-produktiv SQL Server hardware. |
|| Grupper, som skal indbyggede høj tilgængelighed, nedbrud og opgradering til databasen. |Grupper, der kan konfigurere og administrere høj tilgængelighed, nedbrud og rettelse til SQL Server. Nogle angivet automatiseret funktioner dramatisk forenkle dette. |
||Grupper, der ikke ønsker at administrere de underliggende operativsystem og konfigurationsindstillinger.| Hvis du har brug for et tilpasset miljø med fulde administratorrettigheder.|
||Databaser på op til 1 TB, eller større databaser, der kan være [vandret eller lodret opdelt](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) ved hjælp af en skala fra mønster.|Forekomster af SQL Server med op til 64 TB lagerplads. Forekomsten kan understøtte så mange databaser efter behov. |
||[Bygning softwaren som-en-tjenesten (SaaS)-programmer](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Overføre og lave enterprise og hybrid-programmer.|
|||||
|**Ressourcer:**|Du ønsker ikke at anvende IT-ressourcer til konfiguration og administration af den underliggende infrastruktur, men ønsker at fokusere på programlag.|Har du nogle IT-ressourcer til konfiguration og administration. Nogle angivet automatiseret funktioner dramatisk forenkle dette.|
|**Samlede omkostninger for ejerskab:**|Fjerner hardware omkostninger og reducerer administrative omkostninger.|Fjerner hardware omkostninger.|
|**Forretningskontinuitet:**|Ud over indbyggede infrastrukturfunktioner til fejltolerance indeholder Azure SQL-Database funktioner, som [automatiseret sikkerhedskopier](sql-database-automated-backups.md), [Punkt-In-Time gendanne](sql-database-recovery-using-backups.md#point-in-time-restore), [Geografisk-Gendan](sql-database-recovery-using-backups.md#geo-restore)og [Aktive geografisk replikering](sql-database-geo-replication-overview.md) at øge Forretningskontinuitet. Se [Oversigt over SQL-Database business-løbende](sql-database-business-continuity.md)kan finde flere oplysninger.|SQL Server på Azure FOS kan du konfigurere en høj tilgængelighed og genoprettelse efter løsning til genoprettelse til din database specifikke behov. Derfor kan du have et system, der er meget optimeret til dit program. Du kan teste og køre failover ved selv når det er nødvendigt. Få mere at vide under [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Hybrid skyen:**|Dit lokale program kan få adgang til data i Azure SQL-Database.|Med SQL Server på Azure FOS, kan du få programmer, der kører delvist i skyen og delvist on-premises. For eksempel kan du udvide dit lokale netværk og Active Directory-domæne i skyen via [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md). Desuden kan du gemme lokalt datafiler i Azure-lager ved hjælp af [SQL Server-datafilerne i Azure](http://msdn.microsoft.com/library/dn385720.aspx). Se [Introduktion til SQL Server 2014 hybride skyen](http://msdn.microsoft.com/library/dn606154.aspx)kan finde flere oplysninger.|
||Understøtter [SQL Server-transaktions replikering](https://msdn.microsoft.com/library/mt589530.aspx) som et abonnement til at kopiere data.|Fuld understøttelse af [ [SQL Server-transaktions replikering](https://msdn.microsoft.com/library/mt589530.aspx), AlwaysOn tilgængelighed grupper](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), Integration Services, og Log levering for at gentage data. Desuden understøttes traditionelle SQL Server-sikkerhedskopier fuldt ud|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Business motivationer til valg af Azure SQL-Database eller SQL Server på Azure FOS

### <a name="cost"></a>Omkostninger

Uanset om du er en start, der er strapped for kontant eller en gruppe i et eksisterende firma, der kører under tæt budget begrænsninger, er begrænset midler ofte den primære driver når du beslutter, hvordan du kan være vært for dine databaser. I dette afsnit, skal du lære om faktureringen og licenser grundlæggende funktioner i Azure forbindelse med disse to muligheder for relationel database: SQL-Database og SQL Server på Azure FOS. Du kan også lære om beregning af de samlede programmet omkostninger.

#### <a name="billing-and-licensing-basics"></a>Fakturering og licenser grundlæggende

**SQL-Database** er solgt til kunder som en tjeneste, ikke med en licens.  [SQL Server på Azure FOS](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) sælges med en inkluderet licens, som du betaler-minutters. Hvis du har en eksisterende licens, kan du også bruge den.  

I øjeblikket, er **SQL-Database** tilgængelig i flere service lag, som alle faktureres hver time med en fast rente, der er baseret på webtjenesten og ydeevneniveau, du vælger. Derudover kan faktureres til udgående trafik på internettet på almindelig [dataoverførsel satser](https://azure.microsoft.com/pricing/details/data-transfers/). De grundlæggende, Standard og Premium service lag er designet til at levere forudsigelige ydeevne med flere ydeevneniveauer til at matche dit program spidsbelastning krav. Du kan skifte mellem tjenesten niveauer og ydeevneniveauer til at matche dit program varieret overførselshastighed behov. Hvis databasen har høj transaktions lydstyrken og skal understøtte mange samtidige brugere, anbefaler vi webtjenesten Premium. Du kan finde de seneste oplysninger om de aktuelle tjeneste lag [Azure SQL Database Service niveauer](sql-database-service-tiers.md). Du kan også oprette [elastiske database grupper](sql-database-elastic-pool.md) for at dele ydeevne ressourcer mellem databaseforekomster.

Med **SQL-Database**, er database softwaren automatisk konfigureret, installeret for at udbedre og opgraderet fra Microsoft, hvilket reducerer omkostningerne administration. Desuden hjælper dens [indbyggede sikkerhedskopiering](sql-database-automated-backups.md) funktioner dig med at opnå betydeligt penge, især hvis du har et stort antal databaser.

Du kan bruge en af de platform har udleveret SQL Server-billeder (som indeholder en licens) eller flytte din SQL Server-licens med **SQL Server på Azure FOS**. Alle understøttede SQL Server-versioner (2008R2, 2012, 2014, 2016) og versioner (udvikler Express Web, Standard, og Enterprise) er tilgængelig. Desuden er Placer din-ejer – licenser versioner (BYOL) af billederne, der tilgængelige. Når du bruger Azure Hvis billeder, funktionel omkostningerne afhænger af på VM størrelse og udgaven af SQL Server, du vælger. Uanset VM størrelse eller udgave af SQL Server, skal betale du hver minutters licenser omkostninger for SQL Server og Windows Server, sammen med Azure-lager omkostninger om en VM disk. Indstillingen-minutters faktureringsadministrator kan du bruge SQL Server til, så længe du har brug for uden køb addition SQL Server licenser. Hvis du flytter dine egne SQL Server-licens til Azure, betaler du til Windows Server og kun lagerplads omkostninger. Du kan finde flere oplysninger om sætter-dine-ejer licenser, [Licens mobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Beregning af programmet på computeren samlede omkostninger

Når du starter ved hjælp af en sky platform, omfatter omkostninger ved at køre programmet udvikling og administration af omkostninger plus de offentlige sky platform serviceomkostninger.

Her er omkostningsberegningen detaljerede for dit program, der kører i SQL-Database og SQL Server på Azure FOS:

**Når du bruger Azure SQL-Database:**

*Samlede omkostninger for programmet = meget minimerede administrationsomkostninger + software development omkostninger + SQL-Database serviceomkostninger*

**Når du bruger SQL Server på Azure FOS:**

*Samlede omkostninger for programmet = meget minimerede software development omkostninger + administrationsomkostninger + SQL Server og Windows Server licensering omkostninger + Azure lagerplads omkostninger*

Du kan finde flere oplysninger om priser, i følgende ressourcer:

- [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtuelt priser](https://azure.microsoft.com/pricing/details/virtual-machines/) til [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) og til [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure priser Lommeregner](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Der er en lille del af funktioner på SQL Server, der ikke er gældende eller ikke er tilgængelige med SQL-Database. Se [SQL Database generelt begrænsninger og retningslinjer](sql-database-general-limitations.md) og [SQL Database Transact-SQL oplysninger](sql-database-transact-sql-information.md) kan finde flere oplysninger. Hvis du flytter en eksisterende SQL Server-løsning i skyen, kan du se [overføre en SQL Server-database til Azure SQL-Database](sql-database-cloud-migrate.md). Når du overfører et eksisterende lokalt SQL Server-program til SQL-Database, kan du overveje at opdatere programmet at drage fordel af de funktioner, der cloud services tilbud. For eksempel kan du overveje at bruge [Azure-App webtjeneste](https://azure.microsoft.com/services/app-service/web/) eller [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) til at hoste din programlag for at øge omkostninger fordele.

### <a name="administration"></a>Administration

Beslutning til overgang til en skybaseret tjeneste er for mange virksomheder, som det er meget om overføre kompleksiteten af administration som den omkostninger. Med **SQL-Database**administrerer Microsoft den underliggende hardware. Microsoft automatisk kopieres alle data for at sikre høj tilgængelighed, konfigurerer og opgraderer database-software, administrerer belastning og betyder gennemsigtig failover, hvis der er en serverfejl. Du kan fortsætte med at administrere din database, men du ikke længere skal bruge til at administrere databaseprogram, server-operativsystemet eller hardware.  Elementer, du kan fortsætte med at administrere og eksempler på databaser og logon, indeks og justering af forespørgsel, og overvågning sikkerhed.

Med **SQL Server på Azure FOS**har du fuld kontrol over operativsystem og konfiguration af SQL Server-forekomst. Med en VM er det dig med at beslutte, hvornår du skal opdatere/opgradere operativsystemet og database-software og hvornår du skal installere yderligere software som anti-virus. Nogle automatiseret funktioner er angivet til at forenkle dramatisk rettelse, sikkerhedskopiering og høj tilgængelighed. Desuden kan du styre størrelsen på VM, antallet af diske og deres lagerplads konfigurationer. Azure kan du ændre størrelsen på en VM efter behov. Finde [virtuelt og skyen Service størrelser til Azure](../virtual-machines/virtual-machines-linux-sizes.md)på oplysninger. 

### <a name="service-level-agreement-sla"></a>Serviceniveauaftale (SLA)

For mange IT-afdelinger er møde op på oprettelsestidspunkt forpligtelser af en serviceaftale (SLA) højeste prioritet. I dette afsnit skal se vi på hvad SLA gælder for hver database, der er vært for indstilling.

Microsoft tilbyder en tilgængelighed SLA 99,99% Basic **SQL-Database** , som Standard og Premium service niveauer. Du kan finde de seneste oplysninger [Serviceaftale](https://azure.microsoft.com/support/legal/sla/sql-database/). Du kan finde de seneste oplysninger om SQL-Database service niveauer og løbende understøttede forretningsplaner [Service niveauer](sql-database-service-tiers.md).

Microsoft yder til **SQL Server kører på Azure FOS**, en tilgængelighed SLA 99.95%, der dækker kun den virtuelle maskine. Denne SERVICENIVEAUAFTALE omhandler ikke processer (såsom SQL Server), der kører på VM og kræver, at du er vært for mindst to VM forekomster i et sæt tilgængelighed. Du kan finde de seneste oplysninger [VM SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Database høj tilgængelighed (HA) i FOS, skal du konfigurere en af indstillingerne understøttede høj tilgængelighed i SQL Server, som [AlwaysOn tilgængelighed grupper](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Ved hjælp af en indstilling for understøttede høj tilgængelighed indeholder ikke en ekstra SERVICENIVEAUAFTALE, men gør muligt at opnå > 99,99% databasetilgængelighed.

### <a name="market"></a>Tid på markedet

**SQL-Database** er den rigtige løsning til skyen designede programmer, når udviklerproduktivitet og hurtigt tid til at markedsføre er afgørende for. Med programmeringsmæssig synes godt om Databaseadministrator funktionalitet er det perfekte til skyen arkitekter og udviklere som den sænker brug for at administrere de underliggende operativsystem og database. Du kan for eksempel bruge [REST-API](http://msdn.microsoft.com/library/azure/dn505719.aspx) og [PowerShell-cmdlet'er](http://msdn.microsoft.com/library/azure/dn546726.aspx) til at automatisere og administrere administrative handlinger for tusindvis af databaser. Funktioner som [Elastiske Database grupper](sql-database-elastic-pool.md) kan du fokusere på programlag og leverer din løsning til marked hurtigere.

**SQL Server kører på Azure FOS** er perfekt, hvis dine eksisterende eller nye programmer kræver store databaser, forbundne databaser eller adgang til alle funktioner i SQL Server eller Windows. Det er også en god Tilpas, når du vil overføre eksisterende lokale programmer og -databaserne til Azure som-er. Da du ikke behøver at ændre den præsentation, programmer og data lag, spare du tid og budget på rearchitecting din eksisterende løsning. I stedet kan du fokusere på overføre alle dine løsninger til Azure og i at gøre nogle ydeevne optimeringer, der muligvis af Azure platform. Du kan finde yderligere oplysninger finder [Ydeevne bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Oversigt

I denne artikel set SQL-Database og SQL Server på virtuelle Azure-computere (FOS) og beskrevet almindelige business motivators, der kan påvirke din beslutning. Følgende er en oversigt over forslag til, du kan overveje:

Vælg **Azure SQL-Database** , hvis:

- Du opretter nye skybaserede programmer at drage fordel af penge, og Angiv optimering af ydeevne, cloud services. Denne fremgangsmåde giver fordelene ved en fuldt administreret skybaseret tjeneste, hjælper med at nederste indledende tid til at markedsføre og kan give langsigtede omkostninger optimering.

- Du vil have Microsoft udføre almindelige management handlinger på dine databaser og kræver stærkere tilgængelighed SLA for databaser.



Vælg **SQL Server på Azure FOS** , hvis:

- Du har eksisterende lokale programmer, du vil overføre eller udvide i skyen, eller hvis du vil oprette enterprise-programmer, der er større end 1 TB. Denne fremgangsmåde giver en fordel ved 100% SQL kompatibilitet, stor databasekapacitet, fuld kontrol over SQL Server og Windows og sikre tunnel til lokale. Denne fremgangsmåde minimerer omkostninger for udvikling og ændringer af eksisterende programmer.

- Du har eksisterende IT-ressourcer og kan i sidste ende ejer rettelse, sikkerhedskopier og database høj tilgængelighed. Bemærk, at nogle automatiseret funktioner dramatisk forenkle disse handlinger. 


## <a name="next-steps"></a>Næste trin
- Se [SQL-Database Selvstudium: oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md) at komme i gang med SQL-Database.
- Se [SQL-Database priser] (https://azure.microsoft.com/pricing/details/sql-database/).
- Se [klargøring en SQL Server virtuel maskine i Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) at komme i gang med SQL Server på Azure FOS.
- Se [SQL Server på en Azure virtuelt: læringssti](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
