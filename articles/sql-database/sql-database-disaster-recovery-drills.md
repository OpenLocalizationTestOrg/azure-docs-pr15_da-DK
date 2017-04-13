<properties 
   pageTitle="SQL-Database nedbrud gendannelse Drills | Microsoft Azure" 
   description="Få mere at vide vejledning og bedste fremgangsmåder til brug af Azure SQL-Database til at udføre nedbrud gendannelse øvelser, der kan hjælpe holde din kommunikations kritiske business programmer tolerant fejl og udfald." 
   services="sql-database" 
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Udføre nedbrud gendannelse analysér

Det anbefales, at validering af programmet parathed for gendannelse arbejdsproces udføres med jævne mellemrum. Bekræfte programmets funktionsmåde og konsekvenserne af datatab og/eller forstyrrelser, failover omfatter er god teknisk praksis. Det er også et krav ved de fleste branchestandarder som en del af business løbende certificering.

Udføre en nedbrud gendannelse analysér består af:

- Simulere data niveau afbrydelse
- Gendanne 
- Validere programmet integritet indlæg gendannelse

Afhængigt af hvordan du [designet dit program for Forretningskontinuitet](sql-database-business-continuity.md), arbejdsprocessen skal udføre analyser kan variere. Nedenfor beskrives de bedste fremgangsmåder, foretage en nedbrud gendannelse analysér i forbindelse med Azure SQL-Database. 

##<a name="geo-restore"></a>Geografisk-Gendan

For at undgå de muligt tab af data, når foretage en nedbrud gendannelse analysér, anbefaler vi udføre analyser ved hjælp af et testmiljø ved at oprette en kopi af produktionsmiljøet og bruge det til at kontrollere programmets failover arbejdsproces.
 
####<a name="outage-simulation"></a>Afbrydelse simulering

Du kan slette eller omdøbe kildedatabasen for at efterligne afbrydelse. Dette medfører connectivity programfejl. 

####<a name="recovery"></a>Gendannelse

- Udføre geografisk gendannelsen af databasen til en anden server som beskrevet [her](sql-database-disaster-recovery.md). 
- Ændre konfigurationen af programmet for at oprette forbindelse til gendannede databaser, og Følg guiden [Konfigurer en database efter genoprettelse](sql-database-disaster-recovery.md) for at fuldføre at genoprettelsen.

####<a name="validation"></a>Datavalidering

- Du kan udføre analyser ved at kontrollere programmet integritet indlæg gendannelse (det vil sige strenge, logon, grundlæggende funktionalitet test eller andre valideringer en del af standardprogrammet signoffs procedurer).

##<a name="geo-replication"></a>Geografisk gentagelse

Til en database, der er beskyttet via geografisk gentagelse omfatter analysér øvelse planlagte failover til den sekundære database. Den planlagte sekundære sikrer, at primært og sekundære databaser forbliver synkroniseret, når rollerne, der er skiftet. I modsætning til den ikke-planlagte sekundære medfører denne handling ikke tab af data, så gå kan udføres i produktionsmiljøet. 

####<a name="outage-simulation"></a>Afbrydelse simulering

Du kan deaktivere webprogrammet eller virtuelt forbindelse til databasen for at efterligne afbrydelse. Dette medfører fejlene i forbindelse til web-klienterne.

####<a name="recovery"></a>Gendannelse

- Kontrollér, at konfigurationen af programmet i området DR peger på den tidligere sekundære, bliver fuldt tilgængelige nye primær. 
- Udføre [planlagt failover](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) for at gøre den sekundære database en ny primær
- Følg guiden [Konfigurer en database efter genoprettelse](sql-database-disaster-recovery.md) for at fuldføre at genoprettelsen.

####<a name="validation"></a>Datavalidering

- Du kan udføre analyser ved at kontrollere programmet integritet indlæg gendannelse (det vil sige strenge, logon, grundlæggende funktionalitet test eller andre valideringer en del af standardprogrammet signoffs procedurer).


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om løbende forretningsscenarier, se [løbende scenarier](sql-database-business-continuity.md)
- Hvis du vil vide mere om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
