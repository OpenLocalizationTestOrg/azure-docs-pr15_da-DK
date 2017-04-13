<properties 
    pageTitle="DocumentDB Database spørgsmål - ofte stillede spørgsmål | Microsoft Azure" 
    description="Få svar på ofte stillede spørgsmål om Azure DocumentDB en NoSQL dokument database tjeneste for JSON. Besvare database spørgsmål om kapacitet, ydeevne og skalering." 
    keywords="Database spørgsmål, ofte stillede spørgsmål, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Ofte stillede spørgsmål om DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Database spørgsmål om Microsoft Azure DocumentDB grundlæggende

### <a name="what-is-microsoft-azure-documentdb"></a>Hvad er Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB er en forrygende hurtig, kloden skalering NoSQL dokument database-som-en-tjeneste, som giver effektive forespørgsels over skema ledig data, hjælper med at levere konfigurerbar og pålidelig ydeevne, og gør det muligt for hurtig udvikling, alt sammen via en administreret platform sikkerhedskopieres til en potens og når af Microsoft Azure. DocumentDB er den rigtige løsning til internettet, mobil, gaming og IoT programmer, når mere forudsigelige overførselshastighed, høj tilgængelighed, lav ventetid og et skema ledig datamodellen er de primære krav. DocumentDB leverer skema fleksibilitet og RTF indeksering via en oprindelig JSON-datamodel, og omfatter transaktions understøttelse af flere dokumenter med integreret JavaScript.  
  
Flere database spørgsmål, svar og instruktioner til installation og via denne tjeneste, under [DocumentDB dokumentation side](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>Hvilken slags database er DocumentDB?
DocumentDB er en NoSQL dokument et overblik database, der gemmer dataene i JSON-formatet.  DocumentDB understøtter indlejrede, din egen-contained-data strukturer, der kan forespørges via en omfattende DocumentDB [SQL-forespørgsel grammatik](documentdb-sql-query.md). DocumentDB giver høj ydeevne transaktions behandling på serversiden JavaScript gennem [gemt procedurer, udløsere, og brugerdefinerede funktioner](documentdb-programming.md). Databasen understøtter også udvikler tunable konsistens niveauer med tilknyttede [ydeevne](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>Har DocumentDB databaser tabeller som en relationel database (RDBMS)?
Nej, gemmer DocumentDB data i samlinger af JSON dokumenter.  Finde oplysninger om DocumentDB ressourcer, se [DocumentDB ressource model og begreber](documentdb-resources.md). Du kan finde flere oplysninger om hvordan NoSQL løsninger som DocumentDB adskiller sig fra relationelle løsninger, [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>Understøtter DocumentDB databaser skema ledig data?
Ja, kan DocumentDB programmer til at gemme vilkårlig JSON-dokumenter uden skemadefinitionen eller tip. Data er direkte tilgængelige for forespørgsel via brugergrænsefladen på DocumentDB SQL-forespørgsel.   

### <a name="does-documentdb-support-acid-transactions"></a>Understøtter DocumentDB SURT transaktioner?
Ja, understøtter DocumentDB mellem dokumenter transaktioner udtrykt som JavaScript gemt procedurer og udløsere. Transaktioner er fastsat til en enkelt partition inden for hver samling og udføres med SURT semantik som alt eller isolerede intet fra andre samtidigt udføre kode og bruger anmodninger.  Hvis undtagelser udløst gennem server side kørsel af JavaScript-programkode, det hele transaktion er annulleret. Du kan finde flere oplysninger om transaktioner, [databasetransaktioner program](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>Hvad er typisk use cases for DocumentDB?  
DocumentDB er et godt valg for nye internettet, mobil, gaming og IoT programmer, hvor automatisk skala, forudsigelige ydeevne, hurtig rækkefølgen af millisekunder svar gange og muligheden for at forespørgsel over skema ledig data er vigtige. DocumentDB bruges til hurtig udvikling og understøttende fortløbende gentagelse af programmet datamodeller. Programmer, der administrerer, der genereres brugerindhold og data er [almindelige brug sager for DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>Hvordan giver mere forudsigelige ydeevne i DocumentDB?
En [anmodning om enhed](documentdb-request-units.md) er mål for kapaciteten i DocumentDB. 1 RU svarer til overførsel af få af et dokument med 1KB. Hver handling i DocumentDB, herunder læser, skriver, SQL-forespørgsler og lagret procedure udførelser har en deterministisk RU værdi baseret på overførselshastigheden kræves for at fuldføre handlingen. I stedet for tanker om CPU, EY og hukommelse og hvordan de hver påvirke dit programoverførselshastighed, kan du betragte med hensyn til en enkelt RU måling.

Hver DocumentDB af websteder kan være reserveret med klargjort overførselshastighed med hensyn til RUs af overførselshastighed sekundet. For programmer af enhver skala, kan du benchmark individuelle anmodninger om at måle deres RU værdier og klargøring af websteder til at håndtere summen af anmodning om enheder på tværs af alle anmodninger. Du kan også skalere op eller indskrænke din samling overførselshastighed som behovet i dit program evolve. Flere oplysninger om anmodningen enheder og for at få hjælp ved fastlæggelse af din samling skal, Læs [administrere ydeevne og kapacitet](documentdb-manage.md) , og prøv [overførselshastighed Lommeregner](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>Er DocumentDB HIPAA kompatibel?
Ja, er DocumentDB HIPAA kompatibel. HIPAA fastlægges krav til brug, tidligt, og beskytte af individuelt identifiable sundhed oplysninger. Se [Microsoft Center for sikkerhed og rettighedsadministration](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)kan finde flere oplysninger.

### <a name="what-are-the-storage-limits-of-documentdb"></a>Hvad er lagergrænser af DocumentDB? 
Der er ingen teoretiske grænse for den samlede mængde af data, der kan gemme en samling i DocumentDB. Hvis du vil gemme mere end 250 GB data i en enkelt samling, kan du [kontakte support](documentdb-increase-limits.md) til at have din konto kvote for øget. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>Hvad er overførselshastighed grænserne for DocumentDB? 
Der er ingen teoretiske grænse for den samlede mængde af overførselshastighed, der kan understøtte en samling i DocumentDB, hvis din arbejdsbyrde må distribueres omkring jævnt mellem et tilstrækkeligt stort antal partition taster. Hvis du vil overskrider 250.000 anmodning enheder/andet per af websteder eller konto, kan du [kontakte support](documentdb-increase-limits.md) til at have din konto kvote for øget. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Hvor meget koster Microsoft Azure DocumentDB?
Se siden [DocumentDB prisoplysninger](https://azure.microsoft.com/pricing/details/documentdb/) for detaljer. DocumentDB Brugsgebyrer bestemmes af antallet af websteder i brug, antallet timer, samlingerne, der er online, og den anvendte lager og klargjort overførsel for hver af websteder. 

### <a name="is-there-a-free-account-available"></a>Er der en gratis konto tilgængelig?
Hvis du er ny bruger af Azure, kan du tilmelde dig en [gratis Azure-konto](https://azure.microsoft.com/free/), som giver dig 30 dage og 200 kan du prøve de Azure tjenester. Eller hvis du har et Visual Studio-abonnement, du er berettiget til [150 i gratis Azure kredit månedsbasis](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) til brug på en hvilken som helst Azure service.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>Hvordan kan jeg få mere hjælp med DocumentDB?
Hvis du har brug for en hvilken som helst Hjælp, skal du Henvend dig til os på [Stakoverløb](http://stackoverflow.com/questions/tagged/azure-documentdb), [Azure DocumentDB MSDN udvikler fora](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)eller planlægge en [1:1 chat med DocumentDB engineering-teamet](http://www.askdocdb.com/). Følg os for at holde dig opdateret om de seneste nyheder og DocumentDB og funktioner, på [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Konfigurere Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Hvordan får jeg tilmelding til Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB er tilgængelig i [Azure Portal][azure-portal].  Først skal du tilmelde dig til et Microsoft Azure-abonnement.  Når du tilmelder dig et Microsoft Azure-abonnement, kan du tilføje en DocumentDB konto til din Azure abonnement. Du kan finde oplysninger om tilføjelse af en DocumentDB-konto, [Opret en DocumentDB database-konto](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>Hvad er en master-nøgle?
En master-nøgle er et sikkerhedstoken at få adgang til alle de ressourcer for en konto. Personer med tasten har læst og skriveadgang til alle ressourcerne på databasekontoen. Vær forsigtig, når du distribuerer master taster. Den primære master-nøgle og sekundær master-nøgle er tilgængelige i bladet **taster ** [Azure Portal][azure-portal]. Få [vist, Kopiér, og Regenerer access-taster](documentdb-manage-account.md#keys)kan finde flere oplysninger om taster.

### <a name="how-do-i-create-a-database"></a>Hvordan opretter jeg en database?
Du kan oprette databaser med [Azure-portalen]() , som beskrevet i [oprette en DocumentDB database](documentdb-create-database.md), en [DocumentDB SDK'er](documentdb-sdk-dotnet.md)eller igennem [REST API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>Hvad er en samling?
En samling er en beholder af JSON dokumenter og den tilknyttede JavaScript-programlogik. En samling er en fakturerbar enhed, hvor [omkostninger](documentdb-performance-levels.md) , bestemmes af overførselshastigheden og storaged bruges. Af websteder kan strækker sig over en eller flere partitioner/servere og kan skaleres til at håndtere næsten ubegrænset mængde lagerplads eller overførselshastighed.

Samlinger er også de fakturering enheder til DocumentDB. Hver af websteder er faktureret hver time baseret på den klargjorte overførselshastighed og anvendt lagerplads. Se [DocumentDB priser](https://azure.microsoft.com/pricing/details/documentdb/)kan finde flere oplysninger.  

### <a name="how-do-i-set-up-users-and-permissions"></a>Hvordan konfigurerer jeg brugere og tilladelser?
Du kan oprette brugere og tilladelser ved hjælp af en af [DocumentDB SDK'er](documentdb-sdk-dotnet.md) eller gennem [RESTEN API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Database spørgsmål om udvikling af mod Microsoft Azure DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb"></a>Hvordan du gør jeg Begynd at udvikle mod DocumentDB?
[SDK'er](documentdb-sdk-dotnet.md) er tilgængelige for .NET, Python, Node.js, JavaScript og Java.  Udviklere kan også udnytte [RESTful HTTP API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx) for at interagere med DocumentDB ressourcer fra en række forskellige platforme og sprog. 

Eksempler for DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md)og [Python](documentdb-python-samples.md) SDK'er er tilgængelige på GitHub.

### <a name="does-documentdb-support-sql"></a>Understøtter DocumentDB SQL?
Forespørgselssprog DocumentDB SQL er en udvidet undersæt af forespørgselsfunktionen, der understøttes af SQL. Forespørgselssprog DocumentDB SQL indeholder RTF hierarkiske og relationelle operatorer og udvidelsesmuligheder af via JavaScript baseret bruger defineret funktioner. JSON grammatik giver mulighed for modeling JSON dokumenter som træer med etiketter som noderne i træet, som bruges af både DocumentDB automatisk indeksering teknikker samt SQL-forespørgsel-dialekten for DocumentDB.  For oplysninger om, hvordan du bruger SQL-grammatik, skal du se [Forespørgslen DocumentDB] [ query] artikel.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>Hvad er de datatyper, der understøttes af DocumentDB?
De enkle datatyper, der understøttes i DocumentDB er den samme som JSON. JSON har en simpel type system, der består af strenge, tal (IEEE754 dobbelt præcision) og booleske værdier - SAND, FALSK, og Null-værdier.  Mere komplekse datatyper som DateTime, Guid, Int64 og geometri repræsenteres både i JSON og DocumentDB gennem oprettelse af indlejrede objekter ved hjælp af {} operatoren og matrixer, der bruger operatoren []. 

### <a name="how-does-documentdb-provide-concurrency"></a>Hvordan giver DocumentDB på dokumentsammenfald?
DocumentDB understøtter optimistisk kontrolelement (OCC) via HTTP enhed mærker eller etags. Hver DocumentDB ressource har en etag, og etag er angivet på serveren, hver gang et dokument opdateres. Etag sidehovedet og den aktuelle værdi er inkluderet i alle svarmeddelelser. Etags kan bruges med overskriften If-Match til at tillade serveren for at beslutte, hvis en ressource skal opdateres. If-Match værdien er værdien etag skal kontrolleres. Hvis værdien etag opfylder værdien server etag, opdateres ressourcen. Hvis etag ikke længere er aktuelt, serveren afviser handlingen med en "HTTP 412 betingelse mislykkedes" svarkode. Klienten skal derefter refetch ressourcen for at få fat på den aktuelle etag værdi for ressourcen. Desuden kan etags bruges med If-None-Match sidehoved til at bestemme, hvis en igen hente af en ressource er nødvendigt. 

For at bruge optimistisk i .NET skal du bruge klassen [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Se [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) i eksemplet DocumentManagement på github for en .NET prøve.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>Hvordan udfører jeg transaktioner i DocumentDB?
DocumentDB understøtter sprog-integreret transaktioner via JavaScript gemt procedurer og udløsere. Alle databasefunktioner i scripts udføres under øjebliksbillede isolationsniveauet fastsat til samlingen, hvis det er en enkelt partition af websteder eller dokumenter med den samme partition nøgleværdi i en samling, hvis samlingen er opdelt. Et øjebliksbillede af dokumentversioner (ETags) er taget i starten af posteringen og anvendt kun, hvis scriptet lykkes. Hvis JavaScript'et opstår en fejl, annulleres posteringen. Se [DocumentDB serversiden programming](documentdb-programming.md) få mere at vide.

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>Hvordan kan jeg flere Indsæt dokumenter til DocumentDB? 
Der er tre måder Sådan masseredigeres Indsæt dokumenter til DocumentDB:

- Data overførselsværktøjet, som beskrevet i [importere data til DocumentDB](documentdb-import-data.md).
- Dokument Explorer i portalen Azure, som beskrevet i [Tilføj flere dokumenter med dokument Stifinder](documentdb-view-json-document-explorer.md#BulkAdd).
- Lagrede procedurer, som beskrevet i [DocumentDB serversiden programmering](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>Understøtter DocumentDB ressource link cachelagring?
Ja, da DocumentDB er en RESTful tjeneste, ressource links kan ikke ændres, og kan cachelagres. DocumentDB klienter kan angive en "If-None-Match" sidehoved for læser mod en ressource som dokument eller af websteder og deres lokale kopierer kun, når serverversionen har ændrer opdatering. 

### <a name="is-a-local-instance-of-documentdb-available"></a>Er en lokal forekomst af DocumentDB tilgængelig?
En lokal forekomst af DocumentDB er ikke tilgængelig på nuværende tidspunkt. Du kan spore status for en lokal emulator og stemme for den på [feedback-forummet](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
