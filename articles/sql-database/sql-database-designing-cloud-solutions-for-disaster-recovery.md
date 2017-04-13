<properties
   pageTitle="Løsninger til genoprettelse efter nedbrud - SQL aktive geografisk-databasereplikering i skyen | Microsoft Azure"
   description="Lær at designe skyen nedbrud gendannelse løsninger til business løbende planlægning med geografisk gentagelse til app sikkerhedskopiering af data med Azure SQL-Database."
   keywords="skyen nedbrud løsninger til genoprettelse efter genoprettelse app sikkerhedskopiering af data, og geografisk-gentagelse, business løbende planlægning"
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
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Designe et program til skyen nedbrud ved hjælp af aktive geografisk-gentagelse i SQL-Database


> [AZURE.NOTE] [Aktive geografisk replikering](sql-database-geo-replication-overview.md) er nu tilgængelig for alle databaser i alle niveauer.



Lær at bruge [Active geografisk-gentagelse](sql-database-geo-replication-overview.md) i SQL-Database til design databaseprogrammer tolerant for internationale fejl og katastrofal afbrydelser. Business løbende planlægning, kan du overveje installation topologien for søgeprogram, serviceniveauaftale du målretter, trafik ventetid og omkostninger. I denne artikel kan vi se på de almindelige programmet mønstre og diskutere fordele og kompromiser for hver indstilling. Du kan finde oplysninger om Active geografisk-replikering med elastiske grupper [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Design mønster 1: aktiv / passiv installation til skyen nedbrud med en samtidig placeret database

Denne indstilling er bedst egnet til programmer med følgende egenskaber:

+ Aktiv forekomst i et enkelt Azure område
+ Stærke afhængighed af skrivebeskyttet (RW) adgang til data
+ Tværs område forbindelsen mellem programmet logik og databasen er ikke acceptable på grund af ventetid og trafik omkostninger    

I dette tilfælde er topologien for søgeprogram installation optimeret til håndtering af internationale nedbrud, når alle programkomponenter påvirkes og skal Sådan flytter du som en enhed. Programmet logik og databasen replikeres til et andet område til geografiske redundans, men de bruges ikke for programmet arbejdsbyrde på normal betingelser. Programmet i det sekundære område skal være konfigureret til at bruge en SQL-forbindelsesstreng til den sekundære database. Trafik manager er konfigureret til at bruge [failover routing metode](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Azure trafik manager](../traffic-manager/traffic-manager-overview.md) bruges i denne artikel kun som illustration. Du kan bruge en hvilken som helst belastning løsning, der understøtter failover routing metode.    

Ud over forekomsterne af hovedvinduet i programmet, skal du overvejer at implementere et lille [arbejder rolle program](cloud-services-choose-me.md#tellmecs) , der overvåger din primære database ved at udstede periodiske T-SQL skrivebeskyttet (RO)-kommandoer. Du kan bruge det til automatisk for at udløse failover, til at generere en besked på dit program administrationskonsollen eller begge dele. For at sikre, at overvågning ikke der påvirkes af region hele udfald, skal du installere de overvågning forekomster af tjenesteprogrammer til hvert område og har hver forekomst, der er knyttet til den primære database i området primære og sekundære databasen i det lokale område. 

> [AZURE.NOTE] Både overvågning programmer skal være aktiv og forespørgsler både primære og sekundære databaser. Disse kan bruges til at registrere en fejl i det sekundære område og den besked, når programmet ikke er beskyttet.     

I følgende diagram vises denne konfiguration før en afbrydelse.

![Konfiguration af SQL Database geografisk-gentagelse. Nedbrud i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Når du har en afbrydelse i den primære region registrerer programmet overvågning, den primære database er ikke tilgængelig og registrerer en besked. Afhængigt af dit program SERVICENIVEAUAFTALE, kan du bestemme, hvor mange fortløbende overvågning sonder går ned, før den erklærer afbrydelse en database. For at opnå koordineret failover af programmet slutpunkt og databasen, bør du har overvågning programmet udføre følgende trin:

1. [Opdatere status for det primære slutpunkt](https://msdn.microsoft.com/library/hh758250.aspx) for at udløse slutpunkt failover.
2. Ring til at [starte database failover](sql-database-geo-replication-portal.md)sekundær databasen.

Programmet behandler bruger anmodningerne i det sekundære område efter failover men forbliver samtidig placeret med databasen, da den primære database vil nu være i det sekundære område. Dette scenarie illustreres ved næste diagrammet. I alle diagrammer streger angiver aktive forbindelser, punkterede linjer angiver afbrudt forbindelser og stop fortegn angiver handling udløsere.


![Geografisk-gentagelse: Failover til sekundær database. App sikkerhedskopiering af data.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Hvis en afbrydelse sker der i det sekundære område, gentagelse sammenkædningen mellem primært og sekundære databasen er afbrudt og overvågning programmet registrerer en besked om, at den primære database, der vises. Programmets ydeevne påvirkes ikke, men programmet fungerer udsatte og derfor på højere risiko i tilfælde begge områder mislykkes efter hinanden.

> [AZURE.NOTE] Vi anbefaler kun installationskonfigurationer med et enkelt DR område. Det skyldes, at de fleste af de Azure lande har to områder. Disse konfigurationer beskytter ikke dit program opstår nedbrud af begge områder. I en sandsynlighed hændelse af sådanne en fejl, kan du gendanne dine databaser i en tredje region, ved hjælp af [geografisk gendannelsen](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Når afbrydelse er afhjulpet, synkroniseres den sekundære database automatisk med primært. Under synkroniseringen kan ydeevnen af primært være lidt påvirkes afhængigt af mængden data, der skal synkroniseres. I følgende diagram vises en afbrydelse i det sekundære område.

![Sekundær databasen synkroniseres med primær. Nedbrud i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


De vigtigste **fordele** ved designmønsteret er:

+ SQL-forbindelsesstrengen er angivet under implementeringen programmet i hver region og ændre ikke efter failover.
+ Programmets ydelsen påvirkes ikke af failover som programmet, og databasen er altid samtidig placeret.

Den primære **fordeling** er, at den overflødige programmet forekomst i det sekundære område bruges kun til nedbrud.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Design mønster 2: aktiv-aktiv installation af programmet belastning
Denne indstilling for skyen nedbrud gendannelse er bedst egnet til programmer med følgende egenskaber:

+ Høj forholdet mellem database læser til skriver
+ Database Skriv ventetid påvirker ikke slutbrugeroplevelsen  
+ Skrivebeskyttet logik kan være adskilt fra læse / skrive-logik ved hjælp af en anden forbindelsesstreng
+ Skrivebeskyttet logik afhænger ikke data bliver synkroniseret fuldt ud med de seneste opdateringer  

Hvis dine programmer har disse egenskaber, kan belastningsjustering slutbrugerlicensaftale forbindelser på tværs af flere forekomster af tjenesteprogrammer i forskellige områder forbedre ydeevnen og slutbrugerens oplevelse. For at implementere justering af belastning, skal hvert område har en aktiv forekomst af programmet med læse-og skriveadgang (RW) logik forbindelse til den primære database i det primære område. Skrivebeskyttet (RO) logik skal have forbindelse til en sekundær database i det samme område som forekomsten af programmet. Trafik manager skal konfigureres til at bruge [round robin - routing](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) - eller [distribution af ydeevne](../traffic-manager/traffic-manager-configure-performance-routing-method.md) med [slutpunkt overvågning](../traffic-manager/traffic-manager-monitoring.md) aktiveret for hver forekomst af programmet.

Som mønster #1, bør du overveje at implementere et lignende overvågning program. Men i modsætning til mønster #1, overvågning programmet er ikke ansvarlig for udløsning sekundære slutpunkt.

> [AZURE.NOTE] Mens denne mønster bruger mere end én sekundær database, vil kun én af secondaries bruges til failover på grund af nævnt tidligere. Da denne mønster kræver skrivebeskyttet adgang til sekundært, kræver det aktive geografisk-gentagelse.

Trafik manager skal konfigureres til ydeevne routing for at dirigere brugerforbindelser til den programmet forekomst, der er tættest på brugerens geografiske placering. I følgende diagram vises denne konfiguration før en afbrydelse.
![Ingen afbrydelse: ydeevne routing til nærmeste programmet på computeren. Geografisk-gentagelse.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Hvis en database afbrydelse allerede findes i det primære område, skal starte du failover af den primære database til en af de sekundære områder, ændre placeringen af den primære database. Trafik manager automatisk udelader offline slutpunkt fra tabellen routing, men stadig routing slutbrugerlicensaftale trafik til de resterende online forekomster. Da den primære database er nu i et andet område, skal alle online forekomster ændre deres læse / skrive-SQL-forbindelsesstreng til at oprette forbindelse til den nye primære. Det er vigtigt, at du foretager denne ændring, før du starter sekundære database. Skrivebeskyttet SQL forbindelse-strenge skal forblive uændret, når de altid pege på databasen i det samme område. Trinnene for failover er:  

1. Ændre læse-og skriveadgang SQL forbindelsesstrenge til at pege på den nye primære.
2. Ring til den angivne sekundære database i rækkefølge Sådan [flytter du Påbegynd database](sql-database-geo-replication-portal.md).

I følgende diagram vises den nye konfiguration efter sekundære.
![Konfiguration efter failover. Nedbrud i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

I tilfælde af en afbrydelse i en af de sekundære områder fjernes trafik manager automatisk offline slutpunkt i det pågældende område fra tabellen routing. Replikering kanalen til den sekundære database i det pågældende område er afbrudt. Da de resterende områder få ekstra bruger trafik i dette scenarie, der påvirkes programmets ydeevne under afbrydelse. Når afbrydelse er afhjulpet, synkroniseres den sekundære database i påvirkede område med det samme med primært. Under synkronisering kan ydeevnen af primært være en smule påvirkes afhængigt af mængden data, der skal synkroniseres. I følgende diagram vises en afbrydelse i en af de sekundære områder.

![Afbrydelse i sekundær område. Nedbrud - geografisk gentagelse i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Vigtige **fordelen** ved denne design mønster er, at du kan skalere programmet arbejdsbelastningen på tværs af flere secondaries at opnå optimal slutbrugerlicensaftale ydeevne. **Kompromiserne** af denne indstilling er:

+ Læse / skrive-forbindelser mellem forekomster af tjenesteprogrammer og databasen har forskellige ventetid og omkostninger
+ Programmets ydeevne påvirkes under afbrydelse
+ Forekomster af tjenesteprogrammer er påkrævet for at ændre dynamisk SQL-forbindelsesstrengen efter database failover.  

> [AZURE.NOTE] En lignende fremgangsmåde kan bruges til at offload specialiserede arbejdsmængder som rapportering job, business intelligence-værktøjer eller sikkerhedskopier. Typisk disse arbejdsbelastninger forbruge betydeligt databaseressourcer derfor det anbefales, at du angive en af de sekundære databaser for dem med de passer til forventede arbejdsbelastningen ydeevne.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Design mønster 3: aktiv / passiv installation til bevaring af data  
Denne indstilling er bedst egnet til programmer med følgende egenskaber:

+ Tab af data er høj business risiko. Sekundære database kan kun bruges som endelig, hvis afbrydelse er permanent.
+ Programmet kan fungere i "skrivebeskyttet tilstand" i et bestemt tidsrum.

I denne mønster skifter programmet til skrivebeskyttet tilstand, når du har forbindelse til den sekundære database. Programlogik i det primære område findes samtidig med den primære database og kører i skrivebeskyttet tilstand (RW). Programlogik i det sekundære område findes samtidig med den sekundære database og er klar til at betjene i skrivebeskyttet tilstand (RO).  Trafik manager skal konfigureres til at bruge [failover distribution](../traffic-manager/traffic-manager-configure-failover-routing-method.md) med [slutpunkt overvågning](../traffic-manager/traffic-manager-monitoring.md) aktiveret for begge forekomster af tjenesteprogrammer.

I følgende diagram vises denne konfiguration før en afbrydelse.
![Aktiv / passiv installation før failover. Nedbrud i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Når trafik manager registrerer en connectivity mislykket til den primære region, skiftes der automatisk bruger trafik til programmet forekomsten i det sekundære område. Det er vigtigt, du **ikke** starte database failover, når afbrydelse er registreret med dette mønster. Programmet i det sekundære område er aktiveret og fungerer i skrivebeskyttet tilstand, der bruger den sekundære database. Dette kan illustreres ved i det følgende diagram.

![Afbrydelse: Programmet i skrivebeskyttet tilstand. Nedbrud i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Når afbrydelse i det primære område er afhjulpet, trafik manager registrerer gendannelse af forbindelse i det primære område og skifter bruger trafik tilbage til programmet forekomsten i det primære område. Forekomsten programmet genoptages og kører i skrivebeskyttet tilstand ved hjælp af den primære database.

> [AZURE.NOTE] Da denne mønster kræver skrivebeskyttet adgang til sekundært kræver aktive geografisk-gentagelse.

I tilfælde af en afbrydelse i det sekundære område trafik manager markerer programmet slutpunkt i den primære region, vises som nedsat og salgskanalen replikering er afbrudt. Denne afbrydelse påvirker dog ikke programmets ydeevne under afbrydelse. Når afbrydelse er afhjulpet, synkroniseres den sekundære database med det samme med primært. Under synkronisering kan ydeevnen af primært være en smule påvirkes afhængigt af mængden data, der skal synkroniseres.

![Afbrydelse: Sekundær database. Nedbrud i skyen.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Dette design mønster har flere **fordele**:

+ Den undgår datatab under midlertidige afbrydelser.
+ Det er ikke nødvendigt at installere en overvågning program, efter at genoprettelsen udløses af trafik manager.
+ Nedetid afhænger af kun hurtigt trafik manager registrerer connectivity fejl, som kan konfigureres.

**Kompromiserne** er:

+ Programmet skal kunne fungere i skrivebeskyttet tilstand.
+ Det er nødvendigt at dynamisk skifte til den, når den er tilsluttet en skrivebeskyttet database.
+ Genoptagelse af læse / skrive-handlinger kræver gendannelse af det primære region, hvilket betyder, at hele dataadgang kan være deaktiveret af timer eller endda dage.

> [AZURE.NOTE] I tilfælde af en permanent service afbrydelse i området, skal du manuelt aktivere database failover og acceptere tab af data. Programmet vil være funktionelle i det sekundære område med læse / skrive-adgang til databasen.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Business løbende planlægning: Vælg et programdesign til skyen nedbrud

Din strategi for bestemte skyen nedbrud gendannelse kan kombinere eller udvide disse designs imødekommer behovet i dit program.  Som tidligere nævnt er den strategi, du vælger baseret på den SERVICENIVEAUAFTALE, du vil tilbyde til dine kunder og topologien for søgeprogram installation. For at hjælpe med at træffe din beslutning, i følgende tabel sammenligner de valgmuligheder, der er baseret på de anslåede datatab eller gendannelse pege mål (frigivne Produktionsordre) og Anslået gendannelse tid (Indsæt).

| Mønster | FRIGIVNE PRODUKTIONSORDRE | INDSÆT
| :--- |:--- | :---
| Installation af aktiv / passiv for nedbrud med samtidig placeret database access | Læse / skrive-adgang < 5 sekund | Fejl ved registrering af tid + failover API ringe + programmet bekræftelse teste
| Installation af aktiv-aktiv for programmet belastning | Læse / skrive-adgang < 5 sekund | Fejl ved registrering af tid + failover API opkald + SQL forbindelsesstreng ændre + programmet bekræftelse teste
| Installation af aktiv / passiv for bevaring af data | Skrivebeskyttet adgang < 5 sec læse / skrive-adgang = nul | Skrivebeskyttet adgang = connectivity manglende registrering af tid + programmet bekræftelse test <br>Læse / skrive-adgang = tid at reducere afbrydelse

## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge Automatiseret sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
- Du kan finde oplysninger om Active geografisk-replikering med elastiske grupper [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
