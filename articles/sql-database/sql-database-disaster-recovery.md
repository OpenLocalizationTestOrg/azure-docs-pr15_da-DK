<properties
   pageTitle="SQL-Database nedbrud | Microsoft Azure"
   description="Lær, hvordan du gendanner en database fra en regionale datacenter strømafbrydelse eller fejl med Azure SQL aktive geografisk-databasereplikering og geografisk-Gendan funktioner."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Gendanne en Azure SQL-Database eller failover til et sekundært

Azure SQL-Database indeholder følgende funktioner for at gendanne fra en afbrydelse:

- [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md)
- [Geografisk-Gendan](sql-database-recovery-using-backups.md#point-in-time-restore)

Se [Forretningskontinuitet](sql-database-business-continuity.md)for at få mere for at vide om forretningsscenarier løbende og de funktioner, der understøtter følgende scenarier.

### <a name="prepare-for-the-event-of-an-outage"></a>Forberede for hændelsen i en afbrydelse

For at opnå succes med gendannelse til et andet dataområde ved hjælp af aktive geografisk replikering eller geografisk overflødige sikkerhedskopier, skal du forberede en server i en anden datacenter afbrydelse at blive den nye primære server skal brug opstår samt har også defineret trin dokumenteret og afprøvet for at sikre en jævn genoprettelse. Disse forberedelse omfatter:

- Identificere den logiske server i et andet område, der skal være den nye primære server. Med aktive geografisk-gentagelse, vil det være mindst én og måske hver af de sekundære servere. For geografisk-Gendan, vil dette normalt være en server i [parvis område](../best-practices-availability-paired-regions.md) til et område, hvor databasen er placeret.
- Identificere, og du kan også definere de serverniveau firewallregler på, der kræves for brugere adgang til den nye primære database.
- Find ud af, hvordan du går til at omdirigere brugere til den nye primære server, f.eks, ved at ændre strenge eller ved at ændre DNS-poster.
- Identificere, og du kan også oprette de logon, der skal være til stede i den overordnede database på den nye primære server, og sikre, at disse logon har relevante tilladelser i den overordnede database, hvis en hvilken som helst. Yderligere oplysninger finder du i afsnittet [SQL-Database security efter nedbrud](sql-database-geo-replication-security-config.md)
- Identificere beskeder om regler, der skal opdateres, hvis du vil knytte til den nye primære database.
- Dokument overvågning konfiguration på den aktuelle primære database
- Udføre en [nedbrud analysere](sql-database-disaster-recovery-drills.md). For at efterligne en afbrydelse for geografisk-Gendan, kan du slette eller omdøbe kildedatabasen for at få forbindelse programfejl. For at efterligne en afbrydelse til aktive geografisk-replikering, kan du deaktivere webprogrammet eller virtuel maskine, der er forbundet med den database eller failover databasen for at få connectity programfejl.

## <a name="when-to-initiate-recovery"></a>Hvornår du skal starte gendannelse

Handlingen gendannelse påvirker programmet. Det kræver, at ændre SQL-forbindelsesstrengen eller omdirigering ved hjælp af DNS, hvilket kan medføre tab af permanent data. Det skal derfor gøres kun, når afbrydelse er sandsynligvis holder længere end dit program gendannelse tid mål. Når programmet installeres i fremstilling skal du udføre regelmæssig kontrol af programmet tilstand og bruge følgende datamærker for at gøre, at genoprettelsen er berettiget:

1.  Permanent forbindelse fejl fra programmet niveau til databasen.
2.  Portalen Azure viser en advarsel om en hændelse i området med generelle virkning.
3.  Azure SQL-databaseserver er markeret, vises som nedsat.

Afhængigt af dit program tolerance til nedetid og mulige business ansvar kan du overveje følgende gendannelse indstillinger.

Brug [Få genoprettelige Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) til at få det nyeste geografisk replikerede gendannelsespunkt.

## <a name="wait-for-service-recovery"></a>Vent på en tjeneste gendannelse

Azure teams arbejde fremføre til at gendanne tjenesten kører som hurtigt som muligt, men afhængigt af roden medføre, at det kan tage timer eller dage.  Hvis dit program kan acceptere længere nedetid kan du bare vente på at genoprettelsen at fuldføre. I dette tilfælde er ingen handling fra din side påkrævet. Du kan se den aktuelle Tjenestestatus på vores [Azure Dashboard over tjenestetilstand](https://azure.microsoft.com/status/). Efter at genoprettelsen af området gendannes programmets tilgængelighed.

## <a name="failover-to-geo-replicated-secondary-database"></a>Failover til geografisk replikerede sekundær database

Hvis dit program nedetid kan medføre business ansvar bruger du bør geografisk replikerede databaser i programmet. Programmet hurtigt gendanne tilgængelighed i et andet område i tilfælde af en afbrydelse aktiveres. Lær, hvordan du [konfigurerer geografisk gentagelse](sql-database-geo-replication-portal.md).

Hvis du vil gendanne tilgængeligheden af databaser skal du starte sekundære til den geografisk replikerede sekundære ved hjælp af en af de understøttede metoder.

Benyt en af følgende vejledninger Sådan flytter du til en sekundær geografisk replikerede database:

- [Failover til en geografisk replikerede sekundær portalen til Azure](sql-database-geo-replication-portal.md)
- [Failover til en geografisk replikerede sekundær ved hjælp af PowerShell](sql-database-geo-replication-powershell.md)
- [Failover til en geografisk replikerede sekundær ved hjælp af T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Gendanne ved hjælp af geografisk-Gendan

Hvis dit program nedetid ikke medfører business ansvar kan du bruge geografisk-Gendan som en metode til at gendanne dit program-databaser. Det opretter en kopi af databasen fra den seneste geografisk overflødige sikkerhedskopi.

Benyt en af følgende vejledninger til geografisk-Gendan en database i et nyt område:

- [Geografisk-Gendan en database til en ny region, ved hjælp af Azure-portalen](sql-database-geo-restore-portal.md)
- [Geografisk-Gendan en database til en ny region, ved hjælp af PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Konfigurere din database efter genoprettelse

Hvis du bruger enten geografisk Replikerings-failover eller geografisk-Gendan til at gendanne fra en afbrydelse, skal du sikre dig, at forbindelsen til de nye databaser korrekt konfigureret, så funktionen normal programmet kan genoptages. Dette er en tjekliste over opgaver for at forberede dit gendannet database fremstilling til.

### <a name="update-connection-strings"></a>Opdatere forbindelsesstrenge

Da din gendannet database er placeret i en anden server, skal du opdatere dit program forbindelsesstreng til at pege på serveren.

Du kan finde flere oplysninger om ændring af forbindelsesstrenge, det relevante udvikling sprog til dit [bibliotek for dataforbindelser](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigureres firewallregler

Du har brug at sikre dig, at de firewallregler, der er konfigureret på serveren og på databasen svarer til dem, der blev konfigureret på den primære server og primære database. Du kan finde flere oplysninger, se [Sådan: konfigurere Firewall-indstillinger (Azure SQL-Database)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Konfigurere logon og Database brugere

Du har brug at sikre dig, at alle de logon, der bruges af programmet findes på serveren, der er vært for databasen gendannet. Du kan finde yderligere oplysninger finder [Sikkerhedskonfiguration for geografisk gentagelse](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Du skal konfigurere og teste din server firewallregler og logon (og deres tilladelser) under en nedbrud gendannelse analysér. Disse serverniveau objekter og deres konfiguration muligvis ikke tilgængelig under afbrydelse.

### <a name="setup-telemetry-alerts"></a>Konfiguration af Telemetri beskeder

Du har brug at sikre, at din eksisterende beskeder om regelindstillinger opdateres for at tilknytte gendannet databasen, og den anden server.

Se [Modtage besked beskeder](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) og [Registrer tjenestetilstand](../monitoring-and-diagnostics/insights-service-health.md)kan finde flere oplysninger om regler for påmindelser database.

### <a name="enable-auditing"></a>Aktivere overvågning

Hvis overvågning er nødvendig for at få adgang til databasen, skal du aktivere overvågning efter at genoprettelsen database. En god indikator, overvågning er påkrævet er, at klientprogrammer bruge sikker forbindelsesstrenge i et mønster med *. database.secure.windows.net. Yderligere oplysninger finder du se [Introduktion til SQL database overvågning](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- Hvis du vil vide mere om business løbende design- og gendannelsesfiler scenarier, se [løbende scenarier](sql-database-business-continuity.md)
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
