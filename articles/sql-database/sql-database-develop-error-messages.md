<properties
    pageTitle="Fejlkoder SQL - database forbindelsesfejl | Microsoft Azure"
    description="Få mere at vide om SQL fejlkoder for SQL-Database klientprogrammer som almindelige database forbindelsesfejl, database kopi problemer og generelle fejl. "
    keywords="SQL-fejlkode access sql database forbindelsesfejl, og sql fejlkoder"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>SQL-fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


I denne artikel vises SQL fejlkoder for SQL-Database klientprogrammer, herunder database forbindelsesfejl, midlertidige fejl (også kaldet midlertidige fejl), ressource styring fejl, problemer med kopi af databasen, elastiske puljen og andre fejl. De fleste kategorier er bestemt til Azure SQL-Database, og gælder ikke for Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Database forbindelsesfejl midlertidige fejl og andre midlertidige fejl

I følgende tabel viser SQL fejlkoderne for forbindelsesfejl tab og andre midlertidige fejl, du kan støde på, når programmet forsøger at få adgang til SQL-Database. For at komme i gang selvstudier om, hvordan du opretter forbindelse til Azure SQL-Database, i [tilslutning til Azure SQL-Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>De mest almindelige database forbindelsesfejl og midlertidige fejl fejl

Azure infrastrukturen har mulighed for at omkonfigurere dynamisk servere, når tunge arbejdsbelastninger opstår i tjenesten SQL-Database.  Denne dynamiske funktionsmåde kan medføre klientprogrammet mister forbindelsen til SQL-Database. Denne type fejlsituation kaldes en *midlertidig fejl*.

Hvis klientprogrammet har forsøg, kan den forsøge at genoprette forbindelsen, efter at give midlertidige fejl tid til at rette sig selv.  Vi anbefaler, at du forsinkelse i 5 sekunder før din første forsøg. Prøver igen efter en forsinkelse, der er mindre end 5 sekunder risici sikkerhedsmæssige skytjenesten. For hver efterfølgende forsøg igen forsinkelsen kan vokse eksponentielt, op til maksimalt 60 sekunder.

Midlertidige fejl fejl manifestet typisk som et af følgende fejlmeddelelser fra din klientprogrammer:

- Database < db_name > på < Azure_instance >-serveren er ikke tilgængelig i øjeblikket. Prøv forbindelsen senere. Hvis problemet fortsætter, skal du kontakte kundesupport og give dem < session_id > session sporing ID

- Database < db_name > på < Azure_instance >-serveren er ikke tilgængelig i øjeblikket. Prøv forbindelsen senere. Hvis problemet fortsætter, skal du kontakte kundesupport og give dem < session_id > session sporing ID. (Microsoft SQL Server, fejl: 40613)

- En eksisterende forbindelse blev tvangsafbrudt af en ekstern vært.

- System.Data.Entity.Core.EntityCommandExecutionException: Der opstod en fejl under udførelse af kommandoen definitionen. Se den indre undtagelse for oplysninger. ---> System.Data.SqlClient.SqlException: Der opstod en transport-niveau fejl, når du modtager resultater fra serveren. (udbyder: Session-udbyder, fejl: 19 - fysisk forbindelse kan ikke bruges)

Kode eksempler på forsøg, i:

- [Dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md) 
- [Handlinger for at rette forbindelsesfejl og midlertidige fejl i SQL-Database](sql-database-connectivity-issues.md)

En diskussion til *blokerer periode* for klienter, der bruger ADO.NET er tilgængelig i [SQL Server forbindelse gruppering af (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Midlertidige fejlkoder for fejl

Følgende fejl er forbigående og skal forsøges i programmet logik 

| Fejlkode | Alvor | Beskrivelse |
| ---: | ---: | :--- |
| 4060 | 16 | Kan ikke åbne databasen "%. & #x2a; ls" anmodet af logon. Logon mislykkedes. |
|40197|17|Tjenesten har fundet en fejl, behandling af din anmodning. Prøv igen. Fejlkode %d.<br/><br/>Du får denne fejl, når tjenesten er nede på grund af software eller opgradering af hardware, hardwarefejl eller andre failover problemer. Fejlkode (%d), der er integreret i meddelelsen til fejl 40197 giver ekstra oplysninger om slags fejl eller failover, der er opstået. Nogle eksempler på fejlen koder er integreret i meddelelsen til fejl 40197 er 40020, 40143, 40166 og 40540.<br/><br/>Genoprette forbindelse til din SQL-databaseserver kan automatisk oprette forbindelse til en sund kopi af databasen. Dit program skal fange 40197, fejlloggen den integrerede fejlkode (%d) i meddelelsen til fejlfinding, og prøve at oprette forbindelse til SQL-Database, indtil ressourcerne, der er tilgængelige, og din forbindelsen er oprettet igen.|
|40501|20|Tjenesten er optaget i øjeblikket. Forsøg anmodningen igen efter 10 sekunder. Hændelsen-ID: %ls. Kode: %d.<br/><br/>*Note:* Du kan finde flere oplysninger i:<br/>• [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md).
|40613|17|Database '%. & #x2a; ls' på serveren '%. & #x2a; ls' er ikke tilgængelig i øjeblikket. Prøv forbindelsen senere. Hvis problemet fortsætter, skal du kontakte kundesupport og give dem session sporing ID '%. & #x2a; ls'.|
|49918|16|Kan ikke behandle anmodning. Der er ikke nok ressourcer til at behandle anmodningen.<br/><br/>Tjenesten er optaget i øjeblikket. Prøv anmodningen senere. |
|49919|16|Processen kan ikke oprette eller opdatere anmodning. For mange oprette eller opdatere igangværende handlinger for abonnement "% ld".<br/><br/>Tjenesten er optaget behandling af flere oprette eller opdatere anmodninger om dit abonnement eller server. Anmodninger om er blokeret for ressource optimering. Forespørge [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) for ventende handlinger. Vent, indtil afventer Opret eller Opdater anmodninger er fuldført eller slette et af dine ventende anmodninger og sende anmodningen igen senere. |
|49920|16|Kan ikke behandle anmodning. For mange handlinger i gang for abonnement "% ld".<br/><br/>Tjenesten er optaget behandling af flere anmodninger om dette abonnement. Anmodninger om er blokeret for ressource optimering. Forespørge [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) for driftsstatus. Vent, indtil ventende anmodninger er fuldført eller slette et af dine ventende anmodninger og sende anmodningen igen senere. |

## <a name="database-copy-errors"></a>Kopiér databasefejl

Følgende fejl kan opstå under kopiering af en database i Azure SQL-Database. Yderligere oplysninger finder du se [kopiere en Azure SQL-Database](sql-database-copy.md).


|Fejlkode|Alvor|Beskrivelse|
|---:|---:|:---|
|40635|16|Klient til IP-adresse '%. & #x2a; ls' er midlertidigt deaktiveret.|
|40637|16|Oprette databasekopi er deaktiveret.|
|40561|16|Databasekopi mislykkedes. Enten kilde‑ eller målliste databasen findes ikke.|
|40562|16|Databasekopi mislykkedes. Kildedatabasen er blevet afvist.|
|40563|16|Databasekopi mislykkedes. Destinationsdatabasen er blevet afvist.|
|40564|16|Databasekopi ikke på grund af en intern fejl. Slip destinationsdatabasen, og prøv igen.|
|40565|16|Databasekopi mislykkedes. Ikke mere end 1 samtidige databasekopi fra samme kilde er tilladt. Slip destinationsdatabasen, og prøv igen senere.|
|40566|16|Databasekopi ikke på grund af en intern fejl. Slip destinationsdatabasen, og prøv igen.|
|40567|16|Databasekopi ikke på grund af en intern fejl. Slip destinationsdatabasen, og prøv igen.|
|40568|16|Databasekopi mislykkedes. Kildedatabasen er utilgængelige. Slip destinationsdatabasen, og prøv igen.|
|40569|16|Databasekopi mislykkedes. Destinationsdatabasen er utilgængelige. Slip destinationsdatabasen, og prøv igen.|
|40570|16|Databasekopi ikke på grund af en intern fejl. Slip destinationsdatabasen, og prøv igen senere.|
|40571|16|Databasekopi mislykkedes på grund af en intern fejl. Slip destinationsdatabasen, og prøv igen senere.|

## <a name="resource-governance-errors"></a>Ressource styring fejl

Følgende fejl er forårsaget af unødvendig brug af ressourcer, mens du arbejder med Azure SQL-Database. Eksempel:

- En transaktion er blevet åbnet for længe.
- En transaktion holder for mange låse.
- Et program forbrug for meget hukommelse.
- Et program forbrug for meget `TempDb` mellemrum.

Relaterede emner:

* Du finder flere oplysninger findes her: [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md)

|Fejlkode|Alvor|Beskrivelse|
|---:|---:|:---|
|10928|20|Ressource-ID: %d. %S grænsen for databasen er. og er nået. Du kan finde flere oplysninger [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>Ressource-ID'ET angiver den ressource, der har nået grænsen. For arbejdstråde, ressource-ID = 1. For sessioner, ressource-ID = 2.<br/><br/>*Note:* Du kan finde flere oplysninger om denne fejl, og hvordan du kan løse problemet:<br/>• [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md). |
|10929|20|Ressource-ID: %d. %S mindste garantien er %d, er maksimumgrænsen, og det aktuelle forbrug for databasen er %d. Serveren er i øjeblikket optaget til at understøtte anmodninger, der er større end %d for denne database. Du kan finde flere oplysninger [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Ellers kan du prøve igen senere.<br/><br/>Ressource-ID'ET angiver den ressource, der har nået grænsen. For arbejdstråde, ressource-ID = 1. For sessioner, ressource-ID = 2.<br/><br/>*Note:* Du kan finde flere oplysninger om denne fejl, og hvordan du kan løse problemet:<br/>• [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md).|
|40544|20|Databasen har nået størrelseskvote dens. Partition eller slette data, slip indeks, eller se dokumentationen til mulige løsninger.|
|40549|16|Sessionen er afsluttet, fordi du har en længerevarende transaktion. Prøv at forkorte din transaktion.|
|40550|16|Sessionen er afsluttet, fordi det er hentet for mange låse. Prøve at læse eller redigere færre rækker i en enkelt transaktion.|
|40551|16|Sessionen er afsluttet på grund af for mange `TEMPDB` brugen. Kan du prøve at ændre din forespørgsel for at reducere diskplads midlertidig tabel.<br/><br/>*Tip:* Hvis du bruger midlertidige objekter, spare plads i den `TEMPDB` database ved at slippe midlertidige objekter, når de er ikke længere er nødvendige for sessionen.|
|40552|16|Sessionen er afsluttet på grund af unødvendig transaktion log mellemrum brug. Kan du prøve at ændre færre rækker i en enkelt transaktion.<br/><br/>*Tip:* Hvis du udfører flere indsætter ved hjælp af den `bcp.exe` utility eller `System.Data.SqlClient.SqlBulkCopy` klasse, prøv at bruge den `-b batchsize` eller `BatchSize` indstillinger for at begrænse antallet rækker kopieret til serveren i hver transaktion. Hvis du genopbygger et indeks med den `ALTER INDEX` -sætning, du prøve at bruge den `REBUILD WITH ONLINE = ON` indstilling.|
|40553|16|Sessionen er afsluttet på grund af for stor hukommelsesbelastning. Kan du prøve at ændre din forespørgsel for at behandle færre rækker.<br/><br/>*Tip:* Reducere antallet af `ORDER BY` og `GROUP BY` handlinger i Transact-SQL-kode reducerer hukommelseskravene i din forespørgsel.|

## <a name="elastic-pool-errors"></a>Elastiske puljen fejl

Følgende fejl er relateret til oprettelse og brug af Elastics grupper.

| Fejlnummer | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Elastiske puljen har nået lagergrænsen. Lagerforbrug for elastiske puljen må ikke overstige (%d) MB. | Begrænsning af elastiske puljen diskplads i MB. | Forsøger at skrive data til en database, når lagergrænse på elastiske puljen er nået. | Overveje at øge DTUs af elastiske puljen, hvis det er muligt for at øge lagergrænsen, reducere opbevaring bruges af individuelle databaser i elastiske gruppen, eller fjern databaser fra elastiske puljen. |
| 10929 | EX_USER | %S mindste garantien er %d, er maksimumgrænsen, og det aktuelle forbrug for databasen er %d. Serveren er i øjeblikket optaget til at understøtte anmodninger, der er større end %d for denne database. Se [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) for at få hjælp. Ellers kan du prøve igen senere. | DTU min i databasen. DTU Maks per database | Det samlede antal samtidige medarbejdere (anmodninger) på tværs af alle databaser i elastiske puljen forsøgte at overskride grænsen på puljen. | Overveje at øge DTUs af elastiske puljen, hvis det er muligt for at øge grænsen arbejder, eller fjern databaser fra elastiske puljen. |
| 40844 | EX_USER | Databasen '%ls' på serveren '%ls' er en '%ls' edition database i en elastiske puljen og kan ikke have en fortløbende kopi relation. | databasenavn, database edition, servernavn | Kommandoen StartDatabaseCopy er udstedt til en ikke-premium db i en elastiske puljen. | Kommer snart |
| 40857 | EX_USER | Elastiske puljen blev ikke fundet for server: '%ls', elastiske puljen navn: '%ls'. | navnet på serveren. elastiske puljen navn | Angivne elastiske puljen findes ikke i den angivne server. | Angiv en gyldig elastiske puljen navn. |
| 40858 | EX_USER | Elastiske puljen '%ls' findes allerede i server: '%ls' | elastiske puljen navn, servernavn | Angivne elastiske puljen findes allerede i den angivne logiske server. | Angiv nye elastiske puljen navn. |
| 40859 | EX_USER | Elastiske puljen understøtter ikke webtjenesten '%ls'. | elastiske puljen webtjenesten | Angivne webtjenesten understøttes ikke til klargøring af elastiske puljen. | Angive den korrekte udgave, eller lad webtjenesten tomt for at bruge webtjenesten standard. |
| 40860 | EX_USER | Elastiske puljen '%ls' og tjenesten målsætning '%ls' kombination er ugyldig. | elastiske puljen navn. Tjenesten niveau målsætning navn | Elastiske puljen og tjenesten mål kan angives sammen, kun, hvis tjenesten mål er angivet som 'ElasticPool'. | Angiv korrekte kombination af elastiske puljen og service mål. |
| 40861 | EX_USER | Database edition ' %. *ls' kan ikke anderledes end den elastiske puljen-webtjenesten som er ' %.* ls. | database edition, elastiske puljen webtjenesten | Database edition er anderledes end webtjenesten elastiske puljen. | Angiv ikke en database edition, som er anderledes end webtjenesten elastiske puljen.  Bemærk, at database udgaven ikke behøver at være angivet. |
| 40862 | EX_USER | Elastiske puljen navn skal angives, hvis formålet elastiske puljen tjenesten er angivet. | Ingen | Elastiske puljen service målsætning identificerer ikke entydigt en elastiske puljen. | Angiv navnet elastiske puljen, hvis bruger formålet elastiske puljen tjeneste. |
| 40864 | EX_USER | DTUs for elastiske puljen skal være mindst (%d) DTUs for webtjenesten "%. * ls'. | DTUs for elastiske puljen; elastiske puljen webtjenesten. | Forsøg på at angive DTUs for elastiske puljen under den mindste grænse. | Prøv at angive DTUs for elastiske ressourcepuljen til mindst grænsen for minimum. |
| 40865 | EX_USER | DTUs for elastiske puljen må ikke overstige (%d) DTUs for webtjenesten "%. * ls'. | DTUs for elastiske puljen; elastiske puljen webtjenesten. | Forsøg på at angive DTUs for elastiske puljen over maksimumgrænsen. | Prøv at indstille DTUs for elastiske puljen til ikke er større end maksimumgrænsen. |
| 40867 | EX_USER | Den maksimale per database DTU skal være på mindst (%d) for webtjenesten "%. * ls'. | DTU Maks i databasen. elastiske puljen webtjenesten | Forsøg på at angive DTU Maks per database under den understøttede grænse. | Overvej at bruge den elastiske puljen webtjenesten, der understøtter den ønskede indstilling. |
| 40868 | EX_USER | Den maksimale per database DTU må ikke overstige (%d) for webtjenesten ' %. * ls'. | DTU Maks i databasen. elastiske puljen webtjenesten. | Forsøg på at angive DTU Maks per database ud over den understøttede grænse. | Overvej at bruge den elastiske puljen webtjenesten, der understøtter den ønskede indstilling. |
| 40870 | EX_USER | DTU min per database må ikke overstige (%d) for webtjenesten "%. * ls'. | DTU min i databasen. elastiske puljen webtjenesten. | Forsøg på at angive DTU min per database ud over den understøttede grænse. | Overvej at bruge den elastiske puljen webtjenesten, der understøtter den ønskede indstilling. |
| 40873 | EX_USER | Antallet af databaser (%d) og DTU min hver database (%d) må ikke overstige DTUs af elastiske samling (%d). | Tal databaser i elastiske puljen; DTU min i databasen. DTUs af elastiske samling. | Forsøger at angive DTU min for databaser i elastiske puljen, der overskrider DTUs af elastiske puljen. | Overveje at øge DTUs af elastiske puljen, eller Formindsk DTU min per database, eller Reducer antallet af databaser i elastiske puljen. |
| 40877 | EX_USER | En elastiske puljen kan ikke slettes, medmindre den ikke indeholder nogen databaser. | Ingen | Elastiske puljen indeholder en eller flere databaser og derfor slettes ikke. | Fjern databaser fra elastiske puljen for at slette den. |
| 40881 | EX_USER | Elastiske puljen ' %. * ls' har nået databasen antal grænsen.  Database Tæl grænsen for elastiske puljen må ikke overstige (%d) til en elastiske puljen med (%d) DTUs. | Navnet på elastiske puljen; grænsen for antallet af elastiske puljen, e DTUs for ressourcepulje. | Forsøger at oprette eller tilføje database til elastiske puljen, når databasen Tæl grænsen på elastiske puljen er nået. | Overveje at øge DTUs af elastiske puljen, hvis det er muligt for at øge grænsen database, eller fjern databaser fra elastiske puljen. |
| 40889 | EX_USER | DTUs eller lagergrænsen for elastiske puljen ' %. * ls' kan ikke formindskes, da, der ikke giver tilstrækkelig lagerplads til dens databaser. | Navnet på elastiske puljen. | Forsøger at mindske lagergrænse på elastiske puljen under dens lagerforbrug. | Overvej at reducere lagerforbrug af individuelle databaser i elastiske puljen, eller fjern databaser fra puljen for at reducere dens DTUs eller lagergrænse. |
| 40891 | EX_USER | DTU min per database (%d) må ikke overstige DTU Maks i databasen (%d). | DTU min i databasen. DTU Maks i databasen. | Forsøg på at angive DTU min per database, der er højere end DTU Maks i databasen. | Kontroller, at DTU min per databaser ikke overstiger DTU Maks i databasen. |
| TBD | EX_USER | Lagerstørrelse for en enkelt database i en elastiske pulje må ikke overstige Maks tillader ' %. * ls' service niveau elastiske puljen. | elastiske puljen webtjenesten | Den maksimale størrelse for databasen overskrider den maksimale størrelse, der tillader webtjenesten elastiske puljen. | Angiv den maksimale størrelse af databasen inden for den maksimale størrelse, der tillader webtjenesten elastiske puljen. |

Relaterede emner:

* [Oprette en elastiske databasen gruppe (C#)](sql-database-elastic-pool-create-csharp.md) 
* [Administrere en elastiske database puljen (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Oprette en elastiske database puljen (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Skærm og administrere en elastiske database puljen (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Generelle fejl

Følgende fejl ikke falder inden for eventuelle tidligere kategorier.

|Fejlkode|Alvor|Beskrivelse|
|---:|---:|:---|
|15006|16|<AdministratorLogin>er ikke et gyldigt navn fordi den indeholder ugyldige tegn.|
|18452|14|Logon mislykkedes. Logon er fra et domæne, der ikke har tillid til og kan ikke bruges med Windows authentication.%. & #x2a; ls (Windows-logon ikke understøttes i denne version af SQL Server).|
|18456|14|Login mislykkedes for brugeren ' %. & #x2a;ls'.%. & #x2a; ls %. & #x2a; ls (logon mislykkedes for bruger "%. & #x2a; ls". Ændring af adgangskoden mislykkedes. Ændring af adgangskode ved logon understøttes ikke i denne version af SQL Server.)|
|18470|14|Login mislykkedes for brugeren '%. & #x2a; ls'. Årsag: Kontoen, der er disabled.%. & #x2a; ls|
|40014|16|Flere databaser kan ikke bruges i samme transaktion.|
|40054|16|Tabeller uden et grupperet indeks, der ikke understøttes i denne version af SQL Server. Oprette et grupperet indeks og prøve igen.|
|40133|15|Denne handling understøttes ikke i denne version af SQL Server.|
|40506|16|Angivne SID er ugyldig for denne version af SQL Server.|
|40507|16|' %. & #x2a; ls' kunne ikke startes med parametre i denne version af SQL Server.|
|40508|16|Brug sætningen understøttes ikke for at skifte mellem databaser. Brug en ny forbindelse til at oprette forbindelse til en anden database.|
|40510|16|Sætningen '%. & #x2a; ls' understøttes ikke i denne version af SQL Server|
|40511|16|Indbygget funktion '%. & #x2a; ls' understøttes ikke i denne version af SQL Server.|
|40512|16|Funktion '%ls' understøttes ikke i denne version af SQL Server.|
|40513|16|Server variable '%. & #x2a; ls' understøttes ikke i denne version af SQL Server.|
|40514|16|'%ls' understøttes ikke i denne version af SQL Server.|
|40515|16|Reference til database og/eller server navn i '%. & #x2a; ls' understøttes ikke i denne version af SQL Server.|
|40516|16|Globale temp objekter understøttes ikke i denne version af SQL Server.|
|40517|16|Reserverede ord eller sætning indstilling '%. & #x2a; ls' understøttes ikke i denne version af SQL Server.|
|40518|16|DBCC kommandoen '%. & #x2a; ls' understøttes ikke i denne version af SQL Server.|
|40520|16|Der kan sikres klasse '% S_MSG' understøttes ikke i denne version af SQL Server.|
|40521|16|Der kan sikres klasse '% S_MSG' understøttes ikke i området server i denne version af SQL Server.|
|40522|16|Database vigtigste '%. & #x2a; ls' type understøttes ikke i denne version af SQL Server.|
|40523|16|Oprettelse af implicitte bruger '%. & #x2a; ls' understøttes ikke i denne version af SQL Server. Oprette eksplicit brugeren før du bruger den.|
|40524|16|Datatypen '%. & #x2a; ls' understøttes ikke i denne version af SQL Server.|
|40525|16|MED '%.ls' understøttes ikke i denne version af SQL Server.|
|40526|16|' %. & #x2a; ls' rækkesættet udbyder ikke understøttes i denne version af SQL Server.|
|40527|16|Sammenkædede servere understøttes ikke i denne version af SQL Server.|
|40528|16|Brugere kan ikke være knyttet til certifikater, asymmetrisk nøgler og Windows-logon i denne version af SQL Server.|
|40529|16|Indbygget funktion '%. & #x2a; ls' i repræsentation kontekst understøttes ikke i denne version af SQL Server.|
|40532|11|Kan ikke åbne server "%. & #x2a; ls" anmodet af logon. Logon mislykkedes.|
|40553|16|Sessionen er afsluttet på grund af for stor hukommelsesbelastning. Kan du prøve at ændre din forespørgsel for at behandle færre rækker.<br/><br/>*Note:* Reducere antallet af `ORDER BY` og `GROUP BY` handlinger i Transact-SQL-kode er med til at reducere hukommelseskravene i din forespørgsel.|
|40604|16|Kan ikke oprette/ALTER DATABASE, fordi det ville overstige kvoten for på serveren.|
|40606|16|Vedhæfte databaser understøttes ikke i denne version af SQL Server.|
|40607|16|Windows-logon, der ikke understøttes i denne version af SQL Server.|
|40611|16|Servere, der kan have højst 128 firewallregler defineret.|
|40614|16|Start IP-adressen på firewallregel må ikke overstige slutningen IP-adresse.|
|40615|16|Kan ikke åbne serveren {0}, der er blevet anmodet af logon. Klient til IP-adresse '{1}' har ikke tilladelse til at få adgang til serveren.  Aktivere adgang, bruge SQL Database-portalen, eller afspille sp_set_firewall_rule på master databasen til at oprette en regel for IP-adresse eller adresseområde.  Det kan tage op til fem minutter, før ændringen træder i kraft.|
|40617|16|Den regelnavn for firewall, der starter med <rule name> er for lang. Maksimale længde er 128.|
|40618|16|Regelnavn for firewall være ikke tom.|
|40620|16|Logon mislykkedes for bruger "%. & #x2a; ls". Ændring af adgangskoden mislykkedes. Ændring af adgangskode ved logon, understøttes ikke i denne version af SQL Server.|
|40627|20|Handling på serveren '{0}' og databasen '{1}' er i gang. Vent et par minutter, før du prøver igen.|
|40630|16|Adgangskodevalidering mislykkedes. Adgangskoden opfylder ikke politik for krav, fordi det er for kort.|
|40631|16|Den adgangskode, du har angivet er for lang. Adgangskoden har ikke mere end 128 tegn.|
|40632|16|Adgangskodevalidering mislykkedes. Adgangskoden opfylder ikke politik for krav, fordi det ikke er nok komplekse.|
|40636|16|Kan ikke bruge et reserveret databasenavn '%. & #x2a; ls' i denne handling.|
|40638|16|Ugyldige abonnement-id < abonnement-id >. Abonnement findes ikke.|
|40639|16|Anmodning om stemmer ikke overens med skemaet: <schema error>.|
|40640|20|Der opstod en uventet undtagelse på serveren.|
|40641|16|Den angivne placering er ugyldig.|
|40642|17|Serveren er optaget. Prøv igen senere.|
|40643|16|Den angivne x-ms-version sidehoved værdi er ugyldig.|
|40644|14|Der kunne ikke tillade adgang til det angivne abonnement.|
|40645|16|Servernavn <servername> må ikke være tomme eller null. Det kan kun består af små bogstaver 'a'-'z', tallene 0-9 og bindestreg. Bindestregen muligvis ikke overlappende eller overvågningssporet i navnet.|
|40646|16|Abonnement-ID må ikke være tom.|
|40647|16|Abonnement < abonnement-id har ikke server servernavn.|
|40648|17|For mange anmodninger er blevet udført. Prøv igen senere.|
|40649|16|Ugyldige indholdstype er angivet. Kun program/xml understøttes.|
|40650|16|Abonnementet < abonnement-id > findes ikke eller er ikke klar til handlingen.|
|40651|16|Kunne ikke oprettes server, fordi abonnementet < abonnement-id > er deaktiveret.|
|40652|16|Kan ikke flytte eller oprette server. Abonnement < abonnement-id > overskrider serverkvote.|
|40671|17|Fejl i kommunikation mellem gatewayen og management-tjenesten. Prøv igen senere.|
|40852|16|Kan ikke åbne databasen ' %. *ls' på serveren ' %.* ls' anmodet af logon. Adgang til databasen er kun tilladt ved hjælp af en sikkerhedsaktiveret forbindelsesstreng. For at få adgang til denne database ved at ændre din forbindelsesstrenge, der indeholder 'sikker' i feltet server fulde - 'servernavn'.database.windows bør .net ændres til 'servernavn'.database. `secure`. windows.net.|
|45168|16|I SQL Azure-systemet er belastning, og placere en øvre grænse på samtidige DB CRUD handlinger for en enkelt server (f.eks., oprette databasen). Den angivne fejlmeddelelse server har overskredet det maksimale antal samtidige forbindelser. Prøv igen senere.|
|45169|16|I SQL azure-systemet er belastning, og placerer en øvre grænse på antallet samtidige server CRUD handlinger for en enkelt abonnement (f.eks., oprette server). Det abonnement, der er angivet i en fejlmeddelelse om har overskredet det maksimale antal samtidige forbindelser, og blev afvist. Prøv igen senere.|

## <a name="related-links"></a>Relaterede links

- [Retningslinjer for og Azure SQL Database generelt begrænsninger](sql-database-general-limitations.md)
- [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md)
