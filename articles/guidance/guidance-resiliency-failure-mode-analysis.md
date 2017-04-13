<properties
   pageTitle="Manglende tilstand analyse | Microsoft Azure"
   description="Retningslinjer for at udføre manglende tilstand analyse til skyen løsninger, der er baseret på Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Azure fleksibilitet vejledning: fejl tilstand analyse

Manglende tilstand analyse (FMA) er en proces i forbindelse med opbygning fleksibilitet i et system, ved at identificere mulige fejl punkter i systemet. FMA skal være en del af arkitektur og design faserne, så du kan oprette nedbrud i systemet fra begyndelsen.

Her er den generelle proces til at udføre en FMA: 

1. Identificere alle komponenter i systemet. Medtage eksterne afhængigheder, f.eks som et id-udbydere, tredjepartstjenester osv.   

2. Identificere eventuelle fejl, der kan opstå for hver komponent. En enkelt komponent har muligvis mere end én fejltilstand. For eksempel skal du overveje Læs fejl og skrive mislykkede forsøg separat, fordi de virkning og mulige afhjælpninger vil være forskellige.

3. Bedømme hver fejl tilstand efter den samlede risiko. Overvej disse faktorer:  

    - Hvad er sandsynligheden for, at fejlen. Er det relativt almindelige? Extrememly sjældne? Du behøver ikke nøjagtige tal. har til formål at rangere prioritet.

    - Hvordan påvirker det på programmet, med hensyn til tilgængelighed, tab af data, omkostninger og business afbrydelser? 

4. For hver fejl tilstand afgør, hvordan programmet skal svare og gendanne. Overvej kompromiserne i omkostnings- og kompleksitet.   

Denne artikel indeholder som udgangspunkt for din FMA proces, et katalog over potentielle fejl tilstande og deres afhjælpninger. Kataloget er organiseret efter teknologi eller Azure service samt en generelle kategori for design af programmet niveau. Kataloget er ikke omfattende, men omhandler mange af core Azure tjenester. 


## <a name="app-service"></a>App Service

### <a name="app-service-app-shuts-down"></a>App Service app lukkes.

**Registrering**. Mulige årsager:

- Forventet lukning
    
    - En operator lukker programmet. for eksempel ved hjælp af portalen Azure.

    - Appen er blevet fjernet, fordi den er inaktiv. (Kun, hvis den `Always On` indstilling er deaktiveret.)

- Uventet lukning

    - Appen går ned.

    - En App-tjenesten VM forekomst bliver utilgængeligt.


Application_End logføring kan Fang app domæne lukning (bløde processen går ned) og er den eneste måde til at opfange programmet domæne lukning.

**Gendannelse**

- Hvis lukningen blev forventet, kan du bruge programmets Luk hændelse til at lukke problemfrit. For eksempel bruge i ASP.NET, den `Application_End` metode.

- Hvis programmet er blevet fjernet, mens inaktiv, genstartes den automatisk på den næste anmodning. Dog vil du betale "kolde start" omkostninger. 

- Hvis du vil forhindre, at programmet fjernes, mens inaktiv, aktivere den `Always On` konfiguration i WebApp. Se [konfigurere webapps i Azure App Service][app-service-configure].

- Hvis du vil forhindre, at en operator lukker appen, skal du angive en ressource Lås med `ReadOnly` niveau. [Lås]ressourcer med Azure ressourcestyring[rm-locks].

- Hvis programmet går ned eller en App-tjenesten VM bliver utilgængeligt, App Service genstarter automatisk appen. 


**Diagnosticering**. Program logfiler og web server logfiler. Se [aktivere logføring for webapps i Azure App Service diagnosticering][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>En bestemt bruger flere gange gør forkert anmodninger eller overloads systemet. 

**Registrering**. Godkende brugere, og Medtag bruger-ID i programmet logfiler.

**Gendannelse**

- Bruge [Azure API Management] [ api-management] til begrænsning anmodninger fra brugeren. Se [Avancerede anmodning (throttling) med Azure API Management][api-management-throttling]

- Blokere brugeren.

**Diagnosticering**. Log alle anmodninger om godkendelse.


### <a name="a-bad-update-was-deployed"></a>En forkert opdatering blev installeret.

**Registrering**. Overvåge programmet tilstanden via Azure-portalen (se [skærm Azure web app ydeevne][app-insights-web-apps]) eller implementere [sundhed slutpunkt overvågning mønster][health-endpoint-monitoring-pattern].

**Gendannelse**. Bruge flere [installation pladser] [ app-service-slots] og vende tilbage til den sidste kendte fungerende installation. Du kan finde flere oplysninger, se [grundlæggende web application referencearkitektur][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>OpenID oprette forbindelse (OIDC)-godkendelse mislykkes.

**Registrering**. Mulige fejl funktionsmåder omfatter:

1. Azure AD er ikke tilgængelig eller ikke kan nås på grund af et netværksproblem. Omdirigering til slutpunktet godkendelse mislykkes, og OIDC program medfører en undtagelse.
2. Azure AD-lejer findes ikke. Omdirigering til godkendelse slutpunktet returnerer en HTTP-fejlkode og OIDC program medfører en undtagelse.
3. Brugeren kan godkende. Ingen strategi for registrering er nødvendig; Azure AD håndterer mislykkede forsøg på logon.

**Gendannelse**

1. Løse ikke-afviklet undtagelser fra programmer.
2. Håndtere `AuthenticationFailed` begivenheder.
3. Omdirigere brugeren til en fejlside.
4. Bruger forsøg.


## <a name="azure-search"></a>Azure søgning

### <a name="writing-data-to-azure-search-fails"></a>Skrivning af data til Azure Søg mislykkes.

**Registrering**. Fange `Microsoft.Rest.Azure.CloudException` fejl.

**Gendannelse**

[Søg .NET SDK] [ search-sdk] automatisk nye forsøg efter midlertidige mislykkede forsøg. Eventuelle undtagelser udløst af klient-SDK skal behandles som fejl permanent.

Prøv igen standardpolitikken bruger eksponentiel back-off. Hvis du vil bruge en politik for forskellige forsøg igen, skal du ringe `SetRetryPolicy` på den `SearchIndexClient` eller `SearchServiceClient` klasse. Du kan finde flere oplysninger, se [Automatisk nye forsøg][auto-rest-client-retry].

**Diagnosticering**. Brug [Søg trafik Analytics][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>Læse data fra Azure søgningen mislykkes.

**Registrering**. Fange `Microsoft.Rest.Azure.CloudException` fejl.

**Gendannelse**

[Søg .NET SDK] [ search-sdk] automatisk nye forsøg efter midlertidige mislykkede forsøg. Eventuelle undtagelser udløst af klient-SDK skal behandles som fejl permanent.

Prøv igen standardpolitikken bruger eksponentiel back-off. Hvis du vil bruge en politik for forskellige forsøg igen, skal du ringe `SetRetryPolicy` på den `SearchIndexClient` eller `SearchServiceClient` klasse. Du kan finde flere oplysninger, se [Automatisk nye forsøg][auto-rest-client-retry].

**Diagnosticering**. Brug [Søg trafik Analytics][search-analytics].



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>Kan ikke læse eller skrive en node.

**Registrering**. Løse undtagelsen. Til .NET-klienter, der bruges ofte `System.Web.HttpException`. Andre klienten muligvis andre typer undtagelse.  Du kan finde yderligere oplysninger finder [Cassandra fejlhåndtering gjort rigtigt](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Gendannelse**

- Hver [Cassandra klient](https://wiki.apache.org/cassandra/ClientOptions) har sin egen forsøg politikker og funktioner. Du kan finde flere oplysninger, se [Cassandra fejlhåndtering gjort rigtigt][cassandra-error-handling].
- Brug en rack-aware installation med data noder fordeles over fejl domæner.
- Installer til flere områder med lokale quorum konsistens. Hvis der opstår fejl permanent, skifte til et andet område.

**Diagnosticering**. Programmet logfiler


## <a name="cloud-service"></a>Skybaseret tjeneste

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Web eller arbejder roller er uventet lukkes.

**Registrering**. [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] hændelse udløses.

**Gendannelse**. Tilsidesætte [RoleEntryPoint.OnStop] [ RoleEntryPoint.OnStop] metode til at rydde problemfrit op. Kan finde flere oplysninger i [Feltet højre metode til at håndtere Azure OnStop begivenheder] [ onstop-events] (blog).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>Læse data fra DocumentDB mislykkes.

**Registrering**. Fange `System.Net.Http.HttpRequestException` eller `Microsoft.Azure.Documents.DocumentClientException`. 

**Gendannelse**

- SDK forsøg automatisk mislykkede forsøg. Hvis du vil angive antallet af nye forsøg og maksimale ventetiden, konfigurere `ConnectionPolicy.RetryOptions`. Undtagelser, der udløser klienten har enten ud over politikken forsøg igen eller er ikke midlertidige fejl. 

- Hvis DocumentDB throttles klienten, returneres fejlen HTTP 429. Kontrollere for statuskoden den `DocumentClientException`. Hvis du får fejl 429 konsekvent, kan du overveje at øge overførselshastighed værdien af samlingen DocumentDB.

- Gentage DocumentDB databasen på tværs af to eller flere områder. Alle kopier er læsbare. Ved hjælp af klienten SDK'er, angive den `PreferredLocations` parameter. Dette er en sorteret liste over Azure områder. Alle læser sendes til den første tilgængelige område på listen. Hvis anmodningen mislykkes, forsøger klienten de andre områder på listen i rækkefølge. Kan finde flere oplysninger under [udvikling med flere områder DocumentDB konti][docdb-multi-region].

**Diagnosticering**. Log alle fejl på klientsiden.


### <a name="writing-data-to-documentdb-fails"></a>Skrive data til DocumentDB opstår.

**Registrering**. Fange `System.Net.Http.HttpRequestException` eller `Microsoft.Azure.Documents.DocumentClientException`. 

**Gendannelse**

- SDK forsøg automatisk mislykkede forsøg. Hvis du vil angive antallet af nye forsøg og maksimale ventetiden, konfigurere `ConnectionPolicy.RetryOptions`. Undtagelser, der udløser klienten har enten ud over politikken forsøg igen eller er ikke midlertidige fejl. 

- Hvis DocumentDB throttles klienten, returneres fejlen HTTP 429. Kontrollere for statuskoden den `DocumentClientException`. Hvis du får fejl 429 konsekvent, kan du overveje at øge værdien for gennemløb i DocumentDB af websteder.

- Gentage DocumentDB databasen på tværs af to eller flere områder. Hvis det primære område mislykkes, skal opgraderes et andet område for at skrive. Du kan også udløse en failover manuelt. SDK understøtter automatisk registrering og routing, så programkode fortsat fungerer, når du har en failover. I perioden, failover (typisk minutter) have skrivning højere ventetid, som SDK finder det nye Skriv område. Kan finde flere oplysninger under [udvikling med flere områder DocumentDB konti][docdb-multi-region].

- Som en reserve fastholdes dokumentet til en sikkerhedskopi kø og behandle køen senere.

**Diagnosticering**. Log alle fejl på klientsiden.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>Læse data fra Elasticsearch mislykkes.

**Registrering**. Fange den relevante undtagelse for bestemt [Elasticsearch klient] [ elasticsearch-client] bruges. 

**Gendannelse**

- Bruge prøv igen. Hver klient har sin egen forsøg politikker. 

- Installere flere Elasticsearch noder og bruge gentagelse til høj tilgængelighed.

Yderligere oplysninger, du [Kører Elasticsearch på Azure][elasticsearch-azure].

**Diagnosticering**. Du kan bruge værktøjer til overvågning af til Elasticsearch, eller log alle fejl på klientsiden med data. Se afsnittet 'Overvågnings' i [Kører Elasticsearch på Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Skrive data til Elasticsearch opstår.

**Registrering**. Fange den relevante undtagelse for bestemt [Elasticsearch klient] [ elasticsearch-client] bruges.  

**Gendannelse**

- Bruge prøv igen. Hver klient har sin egen forsøg politikker. 
 
- Hvis programmet kan acceptere en reduceret konsistens niveau, kan du overveje at skrive med `write_consistency` konfiguration af `quorum`.

Yderligere oplysninger, du [Kører Elasticsearch på Azure][elasticsearch-azure].

**Diagnosticering**. Du kan bruge værktøjer til overvågning af til Elasticsearch, eller log alle fejl på klientsiden med data. Se afsnittet 'Overvågnings' i [Kører Elasticsearch på Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Kø lagerplads

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Skriver en meddelelse til Azure kø lagerplads opstår konsekvent.

**Registrering**. Når *N* gentagne forsøg, mislykkes Skrivehandlingen stadig.

**Gendannelse**

- Gemme dataene i en lokal cache og Videresend skriver til lagerplads senere, når tjenesten bliver tilgængelig.
- Oprette en sekundær kø, og skrive til den pågældende computer, hvis den primære kø ikke er tilgængelig.

**Diagnosticering**. Brug [lagringsmål][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>Programmet kan ikke behandle en bestemt meddelelse fra køen. 

**Registrering**. Bestemte-program. For eksempel meddelelsen indeholder ugyldige data, eller forretningslogikken mislykkes eller anden grund. 

**Gendannelse**

Flytte meddelelsen til en separat kø. Køre en separat proces for at undersøge meddelelser i køen.

Overvej at bruge Azure Service Bus Messaging køer, som giver en [kø til inaktive meddelelser] [ sb-dead-letter-queue] funktionalitet hertil.

Bemærk: Hvis du bruger lagerplads køer med WebJobs, indeholder WebJobs SDK indbyggede behandling af meddelelser. Se, [hvordan du bruger Azure kø storage med WebJobs SDK][sb-poison-message].

**Diagnosticering**. Bruge programmet logføring.


## <a name="redis-cache"></a>Redis Cache

### <a name="reading-from-the-cache-fails"></a>Læse fra cachen mislykkes.

**Registrering**. Fange `StackExchange.Redis.RedisConnectionException`.

**Gendannelse**

1. Prøv igen på midlertidige mislykkede forsøg. Azure Redis cache understøtter indbyggede forsøg gennem se [Redis Cache forsøg retningslinjer][redis-retry].
2. Behandle mislykkede forsøg på permanent som en fundne forekomster, og gå tilbage til den oprindelige datakilde.

**Diagnosticering**. Brug [Redis Cache diagnosticering][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>Skrivning til cachen mislykkes.

**Registrering**. Fange `StackExchange.Redis.RedisConnectionException`.

**Gendannelse**

1. Prøv igen på midlertidige mislykkede forsøg. Azure Redis cache understøtter indbyggede forsøg gennem se [Redis Cache forsøg retningslinjer][redis-retry].
2. Hvis fejlen er permanent, skal du ignorere den og lade andre transaktioner, skrive i cachen senere.

**Diagnosticering**. Brug [Redis Cache diagnosticering][redis-monitor].


## <a name="sql-database"></a>SQL-Database

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Kan ikke oprette forbindelse til databasen i det primære område.

**Registrering**. Forbindelse afbrydes.

**Gendannelse**

Forudsætninger: Databasen skal være konfigureret til aktive geografisk-gentagelse. Se [SQL aktive geografisk-databasereplikering][sql-db-replication].

- For forespørgsler, der læses fra en sekundær replika. 
- For Indsæt og opdateringer manuelt skifte til en sekundær replika. Se [starte en planlagt eller uventet sekundær server til Azure SQL-Database][sql-db-failover].

Replikaen bruger en anden forbindelsesstreng, så du skal opdatere forbindelsesstrengen i dit program.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Klient kører af forbindelser i grupperingen af forbindelser.

**Registrering**. Fange `System.InvalidOperationException` fejl. 

**Gendannelse**

- Prøv derefter igen.
- Isolere forbindelsesgrupper for hver use case som en plan for afhjælpning, så use case ikke kan dominerer alle forbindelser.
- Øge de maksimale forbindelsesgrupper.

**Diagnosticering**. Program logfiler.


### <a name="database-connection-limit-is-reached"></a>Database er-forbindelse nået. 

**Registrering**. Azure SQL-Database begrænser antallet af samtidige medarbejdere, logon og sessioner. Begrænsningerne afhænger af webtjenesten. Få mere at vide i [Azure SQL-Database ressource begrænsninger][sql-db-limits].

Hvis du vil registrere disse fejl, fange `System.Data.SqlClient.SqlException` og kontrollere værdien af `SqlException.Number` SQL-fejlkode. Se en liste over relevante fejlkoder [SQL fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer][sql-db-errors].

**Gendannelse**. Forbigående, betragtes disse fejl, så prøver igen kan muligvis løse problemet. Hvis du klikker på ensartet Vis disse fejl, kan du overveje at skalering databasen.

**Diagnosticering**. - [Sys.event_log] [ sys.event_log] forespørgslen returnerer vellykket databaseforbindelser, mislykkede forbindelser og baglåse.

- Oprette en [regel for beskeder om] [ azure-alerts] for mislykkedes forbindelser.

- Aktivere [SQL-Database overvågning] [ sql-db-audit] og Kontrollér, om mislykkede logonforsøg.


## <a name="service-bus-messaging"></a>Service Bus Messaging

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Læse en meddelelse fra en tjeneste Bus kø mislykkes.

**Registrering**. Løse undtagelser fra klient-SDK. Den grundlæggende klasse for Service Bus undtagelser er [MessagingException][sb-messagingexception-class]. Hvis fejlen er forbigående, den `IsTransient` egenskaben er sand. 

Få mere at vide under [Service Bus beskeder undtagelser][sb-messaging-exceptions].

**Gendannelse**

1. Prøv igen på midlertidige mislykkede forsøg. Se [Service Bus prøv igen retningslinjer][sb-retry].

2. Meddelelser, der ikke kan leveres til en hvilken som helst modtager er placeret i en *kø til inaktive meddelelser*. Brug denne kø til at se, hvilke meddelelser der kunne ikke modtages. Der er ingen automatisk oprydning af den kø til inaktive meddelelser. Meddelelserne forbliver der, indtil du hente dem eksplicit. Se [Oversigt over Service Bus uanbringelige meddelelser][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Skrive en meddelelse til en tjeneste Bus mislykkes kø.

**Registrering**. Løse undtagelser fra klient-SDK. Den grundlæggende klasse for Service Bus undtagelser er [MessagingException][sb-messagingexception-class]. Hvis fejlen er forbigående, den `IsTransient` egenskaben er sand. 

Få mere at vide under [Service Bus beskeder undtagelser][sb-messaging-exceptions].

**Gendannelse**

1. Service Bus klienten forsøg automatisk efter midlertidige fejl. Som standard bruges eksponentiel back-fra. Efter det maksimale antal forsøg eller maksimale timeout-perioden medfører klienten en undtagelse. Du kan finde flere oplysninger, se [Service Bus prøv igen retningslinjer][sb-retry].

2. Hvis kvoten kø overskrides, klienten viser [QuotaExceededException][QuotaExceededException]. Undtagelsesmeddelelsen giver flere detaljer. Tømme nogle meddelelser fra køen før du prøver igen, og Overvej at bruge afbryder mønster for at undgå fortsat forsøg, mens kvoten er overskredet. Kontroller også, at egenskaben [BrokeredMessage.TimeToLive] ikke er angivet for høj. 

3. I et område, der kan forbedres fleksibilitet ved hjælp af [partitioneret køer eller emner][sb-partition]. En ikke-opdelt kø eller emne er tildelt til en SMS store. Hvis denne SMS store er tilgængelig, mislykkes alle handlinger på den pågældende kø eller emne. En partitioneret kø eller emne er opdelt på tværs af flere SMS butikker. 

4. Opret to Service Bus navneområder i forskellige områder yderligere af tolerance, og gentage meddelelser. Du kan bruge enten aktive gentagelse eller passive gentagelse.

    - Aktive gentagelse: klienten sender alle meddelelser til begge køer. Modtageren lytter på begge køer. Mærke meddelelser med et entydigt id, så klienten kan slette dublerede meddelelser.

    - Passive gentagelse: klienten sender meddelelsen til én kø. Hvis der er en fejl, går klienten tilbage til andre køen. Modtageren lytter på begge køer. Denne metode reducerer antallet af dublerede meddelelser, der sendes. Modtageren skal stadig håndtere dublerede meddelelser.

    Du kan finde flere oplysninger, se [GeoReplication eksempel] [ sb-georeplication-sample] og [bedste praksis for Isolations programmer mod Service Bus afbrydelser og nedbrud][sb-outages].


### <a name="duplicate-message"></a>Dublerede meddelelse.

**Registrering**. Undersøge den `MessageId` og `DeliveryCount` egenskaber for meddelelsen.

**Gendannelse**

- Hvis det er muligt, designe meddelelsen behandling, som skal være idempotent. Ellers skal gemme meddelelse id'erne for de meddelelser, der allerede er behandlet, og Markér-ID, før behandling af en meddelelse.

- Aktivere registrering af dubletter, ved at oprette kø med `RequiresDuplicateDetection` indstillet til sand. Med denne indstilling, sletter Service Bus automatisk alle meddelelser, der sendes med det samme `MessageId` som forrige meddelelse.  Bemærk følgende:

    -  Denne indstilling forhindrer dublerede meddelelser lagt i køen. Den forhindrer ikke en modtager i mere end én gang behandling af den samme meddelelse.

    - Dubletter har en tidsramme. Hvis en dublet sendes ud over dette vindue, er det ikke registreret.

**Diagnosticering**. Log dublerede meddelelser.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>Programmet kan ikke behandle en bestemt meddelelse fra køen. 

**Registrering**. Bestemte-program. For eksempel meddelelsen indeholder ugyldige data, eller forretningslogikken mislykkes eller anden grund. 

**Gendannelse**

Der er to måder at fejl skal du tænke på. 

- Modtageren registrerer fejlen. I dette tilfælde flytte meddelelsen til den kø til inaktive meddelelser. Senere køre en separat proces for at undersøge meddelelserne i den kø til inaktive meddelelser.

- Modtageren mislykkes midt behandling af meddelelsen &mdash; for eksempel på grund af en ikke-afviklet undtagelse. Hvis du vil håndtere dette tilfælde, bruge `PeekLock` tilstand. I denne tilstand, hvis låsen udløber, bliver meddelelsen tilgængelig for andre modtagere. Hvis meddelelsen overstiger den maksimale levering antal eller den time-to-live, flyttes meddelelsen automatisk til den kø til inaktive meddelelser.

Du kan finde flere oplysninger, se [Oversigt over Service Bus uanbringelige meddelelser][sb-dead-letter-queue].

**Diagnosticering**. Når programmet flytter meddelelsen til den kø til inaktive meddelelser, du Skriv en begivenhed til loggene programmet på computeren.


## <a name="service-fabric"></a>Tjenesten struktur

### <a name="a-request-to-a-service-fails"></a>En anmodning til en tjeneste mislykkes.

**Registrering**. Returnerer en fejl i tjenesten.

**Gendannelse**

- Find en proxy igen (`ServiceProxy` eller `ActorProxy`), og kald metoden service/Agent igen. 

- **Med høj sikkerhed tjeneste**. Ombryde handlinger på pålidelige af websteder i en transaktion. Hvis der er en fejl, posteringen rulles tilbage. Anmodningen, behandles Hvis der er trukket fra en kø igen. 
 
- **Uden status tjeneste**. Hvis tjenesten fortsætter data til en ekstern store, skal være idempotent alle handlinger.


**Diagnosticering**. Programmets logfil

### <a name="service-fabric-node-is-shut-down"></a>Tjenesten strukturen node lukkes ned.

**Registrering**. Et annulleringen token der overføres til tjenestens `RunAsync` metode. Tjenesten strukturen annullerer opgaven før du lukker noden.

**Gendannelse**. Brug annulleringen tokenet til at registrere lukning. Når tjenesten strukturen anmoder om annulleringen, eventuelt arbejde er færdig, og Afslut `RunAsync` så hurtigt som muligt.

**Diagnosticering**. Programmet logfiler


## <a name="storage"></a>Lagerplads

### <a name="writing-data-to-azure-storage-fails"></a>Skrive dataene til Azure-lager ikke

**Registrering**. Klienten modtager fejl, når du skriver.

**Gendannelse**

1. Prøv derefter igen, til at gendanne fra midlertidige mislykkede forsøg. [Prøv igen politik] [ Storage.RetryPolicies] i klienten SDK håndterer dette automatisk.
2. Implementere afbryder mønster for at undgå forvirrende lagerplads.
3. Hvis N forsøg forsøg ikke, kan du udføre en korrekt reserve. Eksempel:

    - Gemme dataene i en lokal cache og Videresend skriver til lagerplads senere, når tjenesten bliver tilgængelig.
    - Hvis handlingen Skriv var placeret i et transaktions omfang, kompensation posteringen.

**Diagnosticering**. Brug [lagringsmål][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>Læse data fra Azure-lager mislykkes.

**Registrering**. Klienten modtager fejl, når du læser.

**Gendannelse**

1. Prøv derefter igen, til at gendanne fra midlertidige mislykkede forsøg. [Prøv igen politik] [ Storage.RetryPolicies] i klienten SDK håndterer dette automatisk.
2. For RA-GRS lagerplads, hvis det ikke lykkes at læse fra den primære slutpunkt, kan du prøve læse fra den sekundære slutpunkt. Klient-SDK kan håndtere sagen automatisk. Se [Azure-lager gentagelse][storage-replication].
3. Hvis *N* forsøg forsøg ikke, kan du tage en fallback handling til at forringe problemfrit. Hvis en produktbillede ikke kan hentes fra lager, Vis en generisk pladsholderbillede.

**Diagnosticering**. Brug [lagringsmål][storage-metrics].


## <a name="virtual-machine"></a>Virtuelt

### <a name="connection-to-a-backend-vm-fails"></a>Forbindelse til en back-end VM mislykkes.

**Registrering**. Netværk forbindelsesfejl.

**Gendannelse**

- Installer mindst to back end-FOS i et sæt af tilgængelighed, bag en justering af belastning.

- Hvis fejlen forbindelse er midlertidige, nogle gange TCP vil korrekt igen forsøge at sende meddelelsen. 

- Implementere en politik for forsøg igen i programmet på computeren. 

- Implementere [afbryder] for fast eller permanent fejl[ circuit-breaker] mønster.

- Hvis den opkald VM overstiger grænsen netværk udgangspunkt, udfylder udgående køen. Hvis den udgående kø er ensartet fuld, kan du overveje at skalering af. 

**Diagnosticering**. Logføre hændelser på tjenesten grænser.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>VM forekomst bliver utilgængelige eller beskadiget.

**Registrering**. Konfigurere en justering af belastning [sundhed efterprøvning af af] [ lb-probe] , signaler om forekomsten VM er i orden. Efterprøvning af af skal kontrollere, om vigtige funktioner svarer korrekt. 

**Gendannelse**. For hvert niveau af programmet kan sætte flere forekomster af VM i det samme sæt tilgængelighed, og Placer belastningsjustering foran FOS. Hvis sundhed efterprøvning af af mislykkes, holder den justering af belastning sende nye forbindelser til den forekomst, beskadiget.

**Diagnosticering**. – Brug justering af belastning [log analytics][lb-monitor].
- Konfigurere systemet overvågning for at overvåge alle tilstanden overvågning slutpunkter.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Operatoren lukker ved et uheld en VM.

**Registrering**. I/T.

**Gendannelse**. Angive en ressource Lås med `ReadOnly` niveau. [Lås]ressourcer med Azure ressourcestyring[rm-locks].

**Diagnosticering**. Bruge [Azure aktivitet logger][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Kontinuert jobbet stopper, når værten SCM er inaktiv.

**Registrering**. Overføre et annulleringen token til funktionen WebJob. Du kan finde flere oplysninger, se [lukning][web-jobs-shutdown]. 

**Gendannelse**. Aktivere den `Always On` konfiguration i WebApp. Få mere at vide under [køre baggrund opgaver med WebJobs][web-jobs].


## <a name="application-design"></a>Programmet design

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Programmet kan ikke håndtere en samling i indgående anmodninger.

**Registrering**. Afhænger af programmet. Typiske symptomer:

- Webstedet starter og returnerer HTTP 5xx fejlkoder.
- Afhængige tjenester, som database eller lagring, start for at begrænse anmodninger. Se efter HTTP-fejl som HTTP 429 (for mange anmodninger), afhængigt af tjenesten.
- Længde på HTTP-kø vokser.

**Gendannelse**

- Skalere ud til at håndtere øgede belastning. 

- Reducere mislykkede forsøg på at undgå overlappende mislykkede forsøg på forstyrre hele programmet. Afhjælpning strategier omfatter:

    - Implementere [(Throttling) mønster] [ throttling-pattern] for at undgå forvirrende back end-systemer.
    - Bruge [kø-baserede Indlæs udjævning] [ queue-based-load-leveling] bufferen anmodninger og behandle dem i en passende tempo.
    - Prioritere visse klienter. Hvis programmet har gratis og betalte niveauer, begrænses eksempelvis kunder på gratis niveau, men ikke betalt kunder. Se [prioritet kø mønster][priority-queue-pattern].

**Diagnosticering**. Bruge [App Service diagnosticering logføring][app-service-logging]. Bruge en tjeneste som [Azure Log Analytics][azure-log-analytics], [Programmet indsigt][app-insights], eller [Nye Relic] [ new-relic] til lettere at forstå diagnosticeringslogfilerne.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Én af handlingerne i en arbejdsproces eller distribueret transaktion mislykkes.

**Registrering**. Når *N* gentagne forsøg, det stadig ikke lykkes.

**Gendannelse**

- Som en plan for afhjælpning, implementere [Scheduler Agent overordnede] [ scheduler-agent-supervisor] mønster til at administrere hele arbejdsprocessen. 
- Ikke prøv igen på timeout. Der findes en lav succes sats for denne fejl. 
- Kø arbejde for at kunne prøv igen senere.

**Diagnosticering**. Log alle handlinger (vellykkede og mislykkede), herunder kompensation handlinger. Brug korrelations-id'er, så du kan spore alle handlinger i samme transaktion.


### <a name="a-call-to-a-remote-service-fails"></a>Et opkald til en ekstern tjeneste mislykkes.

**Registrering**. HTTP-fejlkode.

**Gendannelse**

1. Prøv igen på midlertidige mislykkede forsøg. 
2. Hvis opkaldet mislykkes, når du forsøger *N* , fallback handle. (Program bestemte.)
3. Implementere [afbryder mønster] [ circuit-breaker] for at undgå overlappende mislykkede forsøg. 

**Diagnosticering**. Log alle fjernopkald mislykkede forsøg.


## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om processen FMA se [spændstighed ved design til skytjenester] [ resilience-by-design-pdf] (PDF-overførsel).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

