<properties 
   pageTitle="Designe skyen løsninger til nedbrud ved hjælp af SQL geografisk-databasereplikering | Microsoft Azure"
   description="Lær at designe din skyen løsning for nedbrud ved at vælge det rigtige failover mønster."
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
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Nedbrud gendannelse strategier for programmer, der bruger SQL-Database elastiske puljen 

Kan de år, vi har lært, skytjenester ikke er sikker og katastrofal hændelser og sker. SQL-Database indeholder en række funktioner til at levere til Forretningskontinuitet for dit program, når disse hændelser indtræffer. [Elastiske grupper](sql-database-elastic-pool.md) og enkeltstående databaser understøtter den samme type muligheder for genoprettelse efter genoprettelse. I denne artikel beskrives flere DR strategier for elastiske grupper udnytte disse SQL-Database business løbende funktioner.

Med henblik på i denne artikel vil vi bruge vedtaget SaaS ISV programmet mønster:

<i>En moderne sky baseret web application bestemmelser en SQL-database for hver slutbruger. ISV har et stort antal kunder og derfor bruger mange databaser, kaldet lejer databaser. Da lejer databaser har typisk uventede aktivitet mønstre, bruger ISV en elastiske puljen til databasen omkostninger meget mere forudsigelige over længere tid. Elastiske puljen forenkler også for administration af ydeevnen, når brugeraktiviteten er højest. Ud over lejer databaser bruger programmet også flere databaser til at administrere brugerprofiler, sikkerhed, indsamling brugsmønstre osv. Tilgængeligheden af de enkelte lejere påvirker ikke programmets tilgængelighed som helhed. Men tilgængelighed og ydeevnen af management databaser er meget vigtigt for programmets funktion, og hvis management databaser er offline hele programmet er offline.</i>  

I resten af papiret beskriver vi de DR strategier, der dækker et område af scenarier fra omkostninger følsomme Start programmerne til dem med strenge tilgængelighedskrav.  

## <a name="scenario-1-cost-sensitive-startup"></a>Scenarie 1. Omkostninger følsomme Start

<i>Jeg er en start-virksomhed og er meget koster store og små bogstaver.  Jeg vil forenkle installation og administration af programmet, og jeg vil gerne have en begrænset SLA til individuelle kunder. Men jeg vil sikre, at programmet som helhed er aldrig offline.</i>

For at opfylde kravet om enkel, skal du installere alle lejer databaser i én elastiske puljen i Azure område efter eget valg og installere management databaser som geografisk replikerede enkeltstående databaser. Brug geografisk-Gendan, der vises ingen ekstra omkostninger til nedbrud af lejere. For at sikre tilgængeligheden af management-databaser, skal de være geografisk replikeres til et andet område (trin 1). Løbende omkostninger for nedbrud gendannelse konfigurationen i dette scenarie er lig med de samlede omkostninger for sekundær databaser. Denne konfiguration er illustreret næste diagrammet.

![Figur 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

I tilfælde af en afbrydelse i den primære region illustreres gendannelse trinnene for at få dit program online ved næste diagrammet.

- Med det samme databaser failover for administration (2) til DR-område. 
- Ændre den programmets forbindelsesstreng til at pege på DR området. Alle nye konti og lejer databaser oprettes i DR område. De eksisterende kunder kan se deres data, der er ikke tilgængelig i øjeblikket.
- Opret elastiske puljen med samme konfiguration som den oprindelige gruppe (3). 
- Brug geografisk-Gendan til at oprette kopier af lejeren databaser (4). Du kan overveje at udløse individuelle gendanner ved slutbrugerlicensaftale forbindelser eller bruge nogle andre programmet bestemte prioritet-skema.

På dette tidspunkt dit program er igen er online i DR område, men nogle kunder vil opleve forsinkelse, når du åbner deres data.

![Figur 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Hvis afbrydelse blev midlertidige, kan det skyldes, at det primære område gendannes af Azure, før alle gendanner er angivet i DR område. I dette tilfælde skal du dirigerer, flytte programmet tilbage til det primære område. Processen tager trinnene illustreret næste diagrammet.
 
- Annullere alle udestående geografisk-Gendan anmodninger.   
- Failover management databaser til det primære område (5). Bemærk: Efter det område genoprettelsen gamle primærfarverne er automatisk blevet secondaries. Nu vil de skifte roller igen. 
- Ændre den programmets forbindelsesstreng til at pege tilbage til det primære område. Nu oprettes alle nye konti og lejer databaser i det primære område. Nogle af vores kunder får vist deres data, der er ikke tilgængelig i øjeblikket.   
- Angive alle databaser i DR puljen til skrivebeskyttet at sikre, at de ikke kan redigeres i DR område (6). 
- For hver database i DR puljen, der er blevet ændret siden gendannelse, omdøbe eller slette de tilhørende databaser i den primære gruppe (7). 
- Kopiér de opdaterede databaser fra puljen DR til den primære gruppe (8). 
- Slet DR programgruppen (9)

På dette tidspunkt være dit program online i det primære område med alle lejer databaser tilgængelig i den primære gruppe.

![Figur 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

De vigtigste **få glæde** af denne strategi er lav løbende omkostninger for overflødige data niveau. Sikkerhedskopier er oprettet automatisk af tjenesten SQL-Database med ingen programmet omskrivning og gratis.  Omkostninger er påløbet kun, når de elastiske databaser er gendannet. **Tage** er, at den komplette gendannelse af alle lejer databaser kan tage betydeligt tid. Det afhænger af Samlet antal gendanner du starter i DR område og samlede størrelse af lejer-databaser. Selvom du prioritere nogle lejere gendanner over andre, kan du konkurrere med alle de andre gendanner, som er startet i samme område, som tjenesten vurdere kommunikation og begrænses for at minimere den overordnede indvirkning på de eksisterende kunder databaser. Desuden kan ikke gendannelse af lejer databaser starte før der oprettes nye elastiske puljen i DR område.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenarie 2. Fuldt program med lagdelte service 

<i>Jeg er et fuldt SaaS program med lagdelte service tilbyder og forskellige SLA prøveversion kunder og betale kunder. For de kunder, prøveversion anvende jeg for at reducere omkostningerne som muligt. Prøveversion kunder kan tage nedetid, men jeg vil reducere dens sandsynligheden for. For de udbetalende kunder er en hvilken som helst nedetid flight risiko. Kunderne er altid få adgang til deres data, så jeg vil sikre dig, der betaler.</i> 

For at understøtte dette scenario, skal du adskille prøveversion lejere fra betalt lejere ved at placere dem i separate elastiske grupper. Prøveversion kunder ville have nederste eDTU per lejer og nederste SLA med længere gendannelse tid. De udbetalende kunder ville være i en gruppe med højere eDTU per lejer og en højere SLA. For at sikre, at den laveste gendannelse tid, skal de udbetalende kunder lejer databaser geografisk replikerede. Denne konfiguration er illustreret næste diagrammet. 

![Figur 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Som det første scenario bliver management databaser helt aktive så du bruger en enkeltstående geografisk replikerede database til den (1). Dette sikrer mere forudsigelige ydeevnen til ny kunde abonnementer, opdateringer af profil og andre handlinger i administration. Det område, hvor primærfarver af management databaser bor er det primære område, og det område, hvor secondaries af management databaser bor bliver DR område.

De udbetalende kunder lejer databaser har aktive databaser i "betalt" puljen klargjort i det primære område. Du skal klargøre en sekundær pulje med det samme navn i DR område. Hver lejer ville være geografisk replikeres til sekundær puljen (2). Dette vil aktivere en hurtig gendannelse af alle lejer-databaser ved hjælp af failover. 

Hvis der opstår en afbrydelse i det primære område, illustreres gendannelse trinnene for at få dit program online i den næste diagram:

![Figur 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Med det samme mislykkes over management databaser til DR område (3).
- Ændre programmets forbindelsesstreng til at pege på DR området. Nu oprettes alle nye konti og lejer databaser i DR område. De eksisterende prøveversion kunder kan se deres data, der er ikke tilgængelig i øjeblikket.
- Failover betalt lejerens databaser til puljen i DR område med det samme gendanne deres tilgængelighed (4). Da sekundære er en hurtig metadata niveau ændring, kan du overveje at en optimering, hvor de enkelte failover udløses efter behov af slutbrugerens forbindelser. 
- Hvis din sekundære eDTU gruppestørrelse var mindre end primært, fordi de sekundære databaser kun påkrævet kapacitet til at behandle loggene Skift, mens det var secondaries, skal du straks øge puljen kapacitet nu til at imødekomme fuld arbejdsbelastningen af alle lejere (5). 
- Opret ny elastiske puljen med det samme navn og den samme konfiguration i DR område for prøveversion kundernes databaser (6). 
- Når prøveversion kundernes puljen er oprettet, kan du bruge geografisk-Gendan til at gendanne de individuelle lejer på prøveperiode databaser til den nye gruppe (7). Du kan overveje at udløse individuelle gendanner ved slutbrugerlicensaftale forbindelser eller bruge nogle andre programmet bestemte prioritet-skema.

Programmet er på dette tidspunkt til onlinetilstand i DR område. Alle betalende kunder har adgang til deres data, mens de prøveversion kunder påvirkes forsinkelse, når adgang til deres data.

Når det primære område er gendannet ved Azure *efter at* du har gendannet programmet i det DR-område, du kan fortsætte med at køre programmet i den pågældende region, eller du kan vælge at mislykkes tilbage til det primære område. Hvis det primære område er gendannet *før* failover processen er fuldført, du bør overveje ikke bekendt tilbage det samme. Failback tager trinnene illustreret i næste diagrammet: 
 
![Figur 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Annullere alle udestående geografisk-Gendan anmodninger.   
- Failover management-databaser (8). Efter det område genoprettelsen havde det gamle primære automatisk blive sekundært. Nu det bliver primært igen.  
- Failover betalt lejeren databaser (9). På samme måde, efter det område genoprettelsen gamle primærfarverne automatisk blive på secondaries. Nu vil de være primærfarverne igen. 
- Angiv de gendannede prøveversion databaser, der er ændret i DR område til skrivebeskyttet tilstand (10).
- For hver database i prøveversionen kunder DR puljen, der er blevet ændret siden gendannelse, omdøbe eller slette den tilsvarende database i prøveversionen kunder primære puljen (11). 
- Kopiér de opdaterede databaser fra puljen DR til den primære gruppe (12). 
- Slet DR programgruppen (13) 

> [AZURE.NOTE] Handlingen failover er asynkron. Det er vigtigt, at du udføre de lejer databaser failover kommando i batches med mindst 20 databaser for at minimere tid, gendannelse. 

De vigtigste **få glæde** af denne strategi er, at det giver den højeste SERVICENIVEAUAFTALE til betalende kunder. Det garanterer også, de nye forsøg frigives, så snart prøveversion DR puljen oprettes. **Tage** er, at denne konfiguration, øger de samlede omkostninger for lejer-databaser ved omkostninger for sekundær DR puljen for betalt kunder. Desuden, hvis sekundær puljen har en anden størrelse, vil betalende kunder opleve nederste ydeevnen efter failover før opgraderingen puljen i DR område er afsluttet. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografisk er distribueret program med lagdelte-tjenesten

<i>Jeg har et fuldt SaaS program med lagdelte service tilbyder. Jeg vil tilbyder en meget tilstanden SLA til mine kunder, der er betalt og minimere risikoen for virkning, når udfald opstå, fordi selv kort afbrydelse kan medføre kunde utilfredshed. Det er vigtigt, at de udbetalende kunder altid kan få adgang til deres data. Forsøgene er gratis, og en SERVICENIVEAUAFTALE findes ikke i prøveperioden.</i> 

Hvis du vil understøtter dette scenario, bør du have tre separate elastiske grupper. To samme størrelse grupper med høj eDTUs per database skal være klargjort i to forskellige områder til at indeholde de betalt kunder lejer databaser. Den tredje gruppe, der indeholder de prøveversion lejere ville have en lavere eDTUs per database og klargøres i en af de to område.

For at garantere den laveste gendannelse tid under udfald udbetalende kundernes lejer skal databaser være geografisk replikerede med 50% af de primære databaser i hver af de to områder. På samme måde ville hver region have 50% af de sekundære databaser. Denne måde, hvis et område er offline kun 50% af betalt kundernes databaser ville påvirkes og ville have Sådan flytter du. De andre databaser skal bevares. Denne konfiguration er vist i følgende diagram:

![Figur 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Som i forrige scenarier bliver management databaser helt aktive så du skal konfigurere dem som enkeltstående geografisk replikerede databaser (1). Dette sikrer mere forudsigelige ydeevnen for de nye kunde-abonnementer, opdateringer af profil og andre handlinger i administration. Område A ville være primære region for administration af databaser, og området, B, der skal bruges til gendannelse af management databaser.

De udbetalende kunder lejer databaser bliver også geografisk replikerede men med primærfarver og secondaries opdele mellem område A og B (2)-område. Denne måde, de primære lejer-databaser, der påvirkes af afbrydelse kan skifte til andet området og bliver tilgængelige. Anden halvdelen af lejer databaser vil ikke blive påvirket overhovedet. 

I næste diagram illustreres gendannelse hvad du skal gøre, hvis der opstår en afbrydelse i område A.

![Figur 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Med det samme mislykkes over management databaserne til område B (3).
- Ændre programmets forbindelsesstreng til at pege på management databaser i område B. ændre administrationen databaser for at sikre, at nye konti og lejer databaser oprettes i område B og de eksisterende lejer databaser findes der også. De eksisterende prøveversion kunder kan se deres data, der er ikke tilgængelig i øjeblikket.
- Failover betalt lejerens databaser til puljen 2 område B for at gendanne straks deres tilgængelighed (4). Da sekundære er en hurtig metadata niveau ændring, kan du overveje at en optimering, hvor de enkelte failover udløses efter behov af slutbrugerens forbindelser. 
- Siden nu indeholder puljen 2 kun primære databaser den samlede arbejdsbyrde i puljen øges, så skal du straks øge dets eDTU størrelse (5). 
- Opret ny elastiske puljen med det samme navn og den samme konfiguration i området B for prøveversion kundernes databaser (6). 
- Når puljen oprettes bruge geografisk-Gendan til at gendanne databasen individuelle lejer på prøveperiode i puljen (7). Du kan overveje at udløse individuelle gendanner ved slutbrugerlicensaftale forbindelser eller bruge nogle andre programmet bestemte prioritet-skema.


> [AZURE.NOTE] Handlingen failover er asynkron. Det er vigtigt, at du udføre de lejer databaser failover kommando i batches med mindst 20 databaser for at minimere tid, gendannelse. 

På dette tidspunkt er dit program igen er online i område B. Alle betalende kunder har adgang til deres data, mens de prøveversion kunder påvirkes forsinkelse, når adgang til deres data.

Når område A er gendannet, skal du beslutte, om du vil bruge område B til prøveabonnement kunder eller failback til at bruge prøveversion kunder puljen i område A. Ét kriterium kan være % af lejer på prøveperiode databaser, der er ændret siden gendannelse. Uanset denne beslutning skal du igen saldo betalt lejere mellem to grupper. i næste diagram vises processen, når de lejer på prøveperiode databaser mislykkes tilbage til område A.  
 
![Figur 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Annullere alle udestående geografisk-Gendan anmodninger til prøveabonnement DR-puljen.   
- Failover management-database (8). Efter det område genoprettelsen havde det gamle primære automatisk blev sekundært. Nu det bliver primært igen.  
- Vælg hvilken betalt lejer databaser mislykkes tilbage Sådan flytter du puljen 1 og påbegynd til deres secondaries (9). Efter det område Genoprettelsen blev alle databaser i puljen 1 automatisk secondaries. Nu bliver 50% af dem primærfarver igen. 
- Reducere størrelsen på puljen 2 til den oprindelige eDTU (10).
- Angive alle gendannes prøveversion databaser i området B til skrivebeskyttet tilstand (11).
- For hver database i prøveversionen DR puljen, der er blevet ændret siden gendannelse Omdøb eller Slet den tilsvarende database i prøveversionen primære puljen (12). 
- Kopiér de opdaterede databaser fra puljen DR til den primære gruppe (13). 
- Slet DR programgruppen (14) 

De vigtigste **fordele** ved denne strategi er:

- Den understøtter den mest tilstanden SERVICENIVEAUAFTALE til betalende kunder, da det sikrer, at en afbrydelse kan påvirke mere end 50% af lejer databaser. 
- Det garanterer, de nye forsøg frigives, så snart for DR puljen oprettes under genoprettelsen. 
- Det kan mere effektiv brug af puljen kapacitet, som er garanti for 50% af sekundær databaser i puljen 1 og 2-puljen, skal være mindre aktiv og derefter de primære databaser.

Den primære **kompromiser** er:

- Handlingerne CRUD mod management databaser har kortere ventetid for slutbrugere, der er oprettet forbindelse til område A end for slutbrugere, der er tilknyttet område B, som de kan udføres på primært management databaser.
- Det kræver mere komplekse designet af management-database. For eksempel skulle hver lejer post har mærket placering, der skal ændres under failover og failback.  
- De udbetalende kunder kan opleve nederste ydeevne end normalt, før opgraderingen puljen i område B er afsluttet. 

## <a name="summary"></a>Oversigt

I denne artikel fokuserer på nedbrud gendannelse strategier for det databaseniveau, der bruges af en SaaS ISV med flere lejer program. Den strategi, du vælger bør være baseret på behovene i programmet, som business modellen, den SERVICENIVEAUAFTALE, du vil tilbyde til dine kunder, budget begrænsning osv … Hver beskrevet strategi skitserer fordele og tage, så du kan få en besked beslutning. Det pågældende program medtages også sandsynligt andre Azure komponenter. Så du skal gennemgå deres business løbende vejledning og dirigerer gendannelse af database-niveauet med dem. Hvis du vil vide mere om administration af gendannelse af databaseprogrammer i Azure, se [design skyen løsninger til nedbrud](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge Automatiseret sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
