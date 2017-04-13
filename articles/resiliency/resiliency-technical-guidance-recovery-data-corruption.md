<properties
   pageTitle="Fleksibilitet tekniske vejledning til at gendanne fra databeskadigelse eller utilsigtet sletning | Microsoft Azure"
   description="Artikel på forstå, hvordan du kan gendanne fra databeskadigelse af data eller sletning af utilsigtede data til og designe tolerant, meget tilgængelige fejl tolerant programmer samt planlægning af nedbrud"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Azure fleksibilitet teknisk vejledning: gendannelse fra databeskadigelse eller utilsigtet sletning

En del af en robust løbende forretningsplan har en plan, hvis dine data bliver beskadiget eller slettes ved et uheld. Følgende er oplysninger om gendannelse, når dataene er beskadiget eller slettes ved et uheld, på grund af programfejl eller operator fejl.

##<a name="virtual-machines"></a>Virtuelle maskiner

Brug [Azure sikkerhedskopi](https://azure.microsoft.com/services/backup/)til at beskytte din virtuelle Azure-computere (også kaldet infrastruktur som en tjeneste FOS) fra programfejl eller utilsigtet sletning. Azure sikkerhedskopi gør det muligt for oprettelse af sikkerhedskopier, der er ensartet på tværs af flere VM diske. Desuden kan sikkerhedskopi samling replikeres på tværs af områder til genopretter fra område tab.

##<a name="storage"></a>Lagerplads

Bemærk, mens Azure-lager leverer data fleksibilitet gennem automatiseret replikaer, dette ikke forhindrer din programkode (eller udviklere/brugere) fra ødelægge data gennem utilsigtede eller utilsigtede sletningen, Opdater osv. Vedligeholde data pålidelighed trods programmet eller bruger fejl kræver mere avancerede teknikker, såsom kopiere dataene til en sekundær lagerplacering med en overvågningslog. Udviklere kan udnytte blob [snapshot egenskab](https://msdn.microsoft.com/library/azure/ee691971.aspx), der kan medføre skrivebeskyttet punkt-in-time snapshot af blob indholdet. Dette kan bruges som basis for en data pålidelighed løsning til Azure-lager BLOB.

###<a name="blob-and-table-storage-backup"></a>BLOB og tabel lagerplads sikkerhedskopi

Mens BLOB og tabeller er meget robust, men de altid repræsenterer den aktuelle tilstand for dataene. Gendannelse fra uønskede ændring eller sletning af data kræver muligvis gendanne data til en tidligere tilstand. Det kan ske ved at drage fordel af de muligheder, Azure til at gemme og bevare punkt-in-time kopier.

Du kan udføre punkt-in-time sikkerhedskopier ved hjælp af [blob snapshot funktion](https://msdn.microsoft.com/library/ee691971.aspx)Azure BLOB. For hver snapshot betaler du kun til opbevaring påkrævet for at gemme forskellene i blob, da sidst snapshot-tilstand. Snapshots er afhængige af eksistensen af den oprindelige blob, de er baseret på, så kopiering til en anden blob eller et andet lagerplads konto anbefales. Dette sikrer, at sikkerhedskopidata korrekt er beskyttet mod utilsigtet sletning. Du kan foretage punkt-in-time Kopier for Azure tabeller til en anden tabel eller til Azure BLOB. Mere detaljeret vejledning og eksempler på udføre programmet niveau sikkerhedskopier af tabeller og BLOB kan findes her:

  * [Beskytte dine tabeller mod programfejl](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Beskytte din BLOB mod programfejl](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Database

Der findes flere indstillinger for [Forretningskontinuitet](../sql-database/sql-database-business-continuity.md) (sikkerhedskopiering og gendannelse) til Azure SQL-Database. Databaser kan kopieres ved hjælp af funktionen [Databasekopi](../sql-database/sql-database-copy.md) eller ved at [eksportere](../sql-database/sql-database-export.md) og [importere](https://msdn.microsoft.com/library/hh710052.aspx) en SQL Server bacpac fil. Databasekopi giver en transaktion ensartede resultater, mens en bacpac (via tjenesten Importér/Eksportér) ikke. Begge disse indstillinger køres som kø-baserede tjenester inden for datacenteret, og de indeholder ikke en gang til fuldførelse SLA.

>[AZURE.NOTE]Kopiér og Importér/Eksportér databaseindstillinger Placer en betydeligt graden af belastning på kildedatabasen. De kan udløse ressourcekonflikter eller (throttling) begivenheder.

###<a name="sql-database-backup"></a>SQL-Database sikkerhedskopi

Punkt-in-time sikkerhedskopier til Microsoft Azure SQL-Database, der opnås ved at [kopiere Azure SQL-database](../sql-database/sql-database-copy.md). Du kan bruge denne kommando til at oprette en en transaktion ensartet kopi af en database på samme logiske databaseserveren eller til en anden server. I begge tilfælde er kopien af databasen fuldt funktionelle og helt uafhængigt af kildedatabasen. Hver kopi, du opretter repræsenterer en indstilling for punkt-in-time gendannelse. Du kan gendanne databasetilstanden helt ved at omdøbe den nye database med navnet på kildedatabasen. Alternativt kan du gendanne et angivet delsæt af data fra den nye database ved hjælp af Transact-SQL-forespørgsler. Du kan finde yderligere oplysninger om SQL-Database, [Oversigt over Forretningskontinuitet med Azure SQL-Database](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server på virtuelle maskiner sikkerhedskopi

Til SQL Server, der bruges med Azure infrastruktur som en tjeneste virtuelle maskiner, (kaldes ofte IaaS eller IaaS FOS), der er to muligheder: traditionel sikkerhedskopier og log levering. Ved hjælp af traditionelle sikkerhedskopier gør det muligt at gendanne til et bestemt tidspunkt, men genoprettelsen er langsom. Gendannelse af traditionelle sikkerhedskopier kræver starter med et indledende fuld sikkerhedskopiering og derefter anvende en hvilken som helst taget bagefter sikkerhedskopier. Den anden mulighed er at konfigurere en logfil over forsendelses session for at forsinke gendannelse af logsikkerhedskopier (for eksempel med to timer). Dette giver et vindue til at gendanne fra fejl, der er foretaget på primært.

##<a name="other-azure-platform-services"></a>Andre Azure-platformstjenester

Nogle Azure-platformstjenester gemme oplysninger i et bruger-kontrolleres lagerplads firma eller en Azure SQL-Database. Hvis konto eller lagerplads ressourcen er slettet eller beskadiget, kan dette kan medføre alvorlige fejl med tjenesten. I disse tilfælde er det vigtigt, at sikkerhedskopiering, der ville gør det muligt at genskabe disse ressourcer, hvis de er blevet slettet eller beskadiget.

Til Azure-websteder og Azure Mobile-tjenester, skal du sikkerhedskopiere og vedligehold de tilknyttede databaser. Til Azure Media Service og virtuelle maskiner, skal du bevare tilknyttede Azure-lager kontoen og alle de ressourcer i den pågældende konto. For eksempel skal virtuelle maskiner, du sikkerhedskopiere og administrere VM diskene i Azure blob-lager.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Kontrollister til databeskadigelse eller utilsigtet sletning

##<a name="virtual-machines-checklist"></a>Virtuelle maskiner tjekliste

  1. Gennemgå sektionen virtuelle maskiner af dette dokument.
  2. Sikkerhedskopiere og vedligehold VM diskene med Azure sikkerhedskopiering (eller din egen system til sikkerhedskopiering ved hjælp af Azure blob-lager og Virtuelle snapshots).

##<a name="storage-checklist"></a>Tjekliste for lager

  1. Gennemse afsnittet lager i dette dokument.
  2. Regelmæssigt sikkerhedskopiere kritiske lagerplads ressourcer.
  3. Overvej at bruge funktionen øjebliksbillede til BLOB.

##<a name="database-checklist"></a>Database tjekliste

  1. Gennemgå sektionen Database af dette dokument.
  2. Oprette punkt-in-time sikkerhedskopier ved hjælp af kommandoen databasekopi.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server på virtuelle maskiner sikkerhedskopi tjekliste

  1. Gennemse den SQL Server på virtuelle maskiner sikkerhedskopi afsnit i dette dokument.
  2. Bruge traditionelle sikkerhedskopiering og gendannelse teknikker.
  3. Oprette en forsinket logfil forsendelses session.

##<a name="web-apps-checklist"></a>Web Apps tjekliste

  1. Sikkerhedskopiere og bevare den tilknyttede database, hvis en hvilken som helst.

##<a name="media-services-checklist"></a>Media Services tjekliste

  1. Sikkerhedskopiere og vedligehold de tilknyttede lagerplads ressourcer.

##<a name="more-information"></a>Få mere at vide

Du kan finde flere oplysninger om sikkerhedskopierings- og funktioner i Azure, se [lagerplads, sikkerhedskopiering og gendannelse scenarier](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en serie, fokuseret på [Azure fleksibilitet teknisk vejledning](./resiliency-technical-guidance.md). Hvis du leder efter flere fleksibilitet, nedbrud og høj tilgængelighed ressourcer, kan du se Azure fleksibilitet teknisk vejledning [Yderligere ressourcer](./resiliency-technical-guidance.md#additional-resources).