<properties 
    pageTitle="Introduktion til DocumentDB, en JSON database | Microsoft Azure" 
    description="Få mere at vide om Azure DocumentDB, en NoSQL JSON-database. Databasekilden dokument er bygget til big data, elastiske skalerbarhed og høj tilgængelighed." 
    keywords="JSON databasen, dokument database"
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
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introduktion til DocumentDB: en NoSQL JSON-Database

##<a name="what-is-documentdb"></a>Hvad er DocumentDB?

DocumentDB er en komplet administreret NoSQL database tjeneste, der er bygget til hurtigt og mere forudsigelige ydeevne, høj tilgængelighed, elastiske skalering, globale fordeling og brugervenlighed udvikling. Som et skema ledig NoSQL database indeholder DocumentDB omfattende og velkendte SQL-forespørgsel-funktioner med ensartet lav latenstider på JSON data - at sikre, at 99% af din læser der leveres under 10 millisekunder og 99% af din skriver er served under 15 millisekunder. Disse entydige fordele foretage DocumentDB et stort egnet til internettet, mobil, gaming, og IoT og mange andre programmer, der skal problemfri skala og global gentagelse.

## <a name="how-can-i-learn-about-documentdb"></a>Hvordan kan jeg få mere at vide om DocumentDB? 

En hurtig måde at få mere at vide om DocumentDB og se det i aktion er at følge disse tre trin: 

1. Se to minuttet [Hvad er DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) videoen, som introducerer fordelene ved at bruge DocumentDB.
2. Se tre minuttallet [Opret DocumentDB på Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) video, der fremhæver hvordan du kan komme i gang med DocumentDB ved hjælp af portalen Azure.
3. Gå til den [Forespørgsel tennisklub](http://www.documentdb.com/sql/demo), hvor du kan gennemgå at få mere at vide om de omfattende forespørgsel funktioner, der er tilgængelige i DocumentDB forskellige aktiviteter. Derefter hoved til fanen sandkassetilstand og køre din egen brugerdefinerede SQL-forespørgsler og eksperimentere med DocumentDB.

Derefter gå tilbage til denne artikel, hvor vi vil gå dybere.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Hvilke funktioner og vigtige funktioner findes der i DocumentDB?  

Azure DocumentDB indeholder følgende vigtige funktioner og fordele:

-   **Elastically SVG overførselshastighed og lager:** Nemt skalere op eller indskrænke DocumentDB JSON databasen til dine behov programmet på computeren. Dine data er gemt på massiv tilstand diske (SSD) til lav forudsigelige latenstider. DocumentDB understøtter beholdere til lagring af JSON-data, der kaldes af websteder, som kan skalere til næsten ubegrænsede lagerplads størrelse og klargjort overførselshastighed. Du kan elastically skalere DocumentDB med mere forudsigelige ydeevne problemfrit som vokser i dit program. 

-   **Med flere område gentagelse:** DocumentDB kopieres transparent dine data til alle områder, du har knyttet til kontoen DocumentDB gør det muligt at udvikle programmer, der kræver global adgang til data samtidig med at kompromiserne mellem konsistens, tilgængelighed og ydeevne, alle med tilsvarende garantier. DocumentDB indeholder gennemsigtige internationale failover med API'er til understøttelse af flere websteder og muligheden for at skalere elastically overførselshastighed og lager over hele verden. Lær mere i [distribuere data globalt med DocumentDB](documentdb-distribute-data-globally.md).

-   **Ad hoc-forespørgsler med velkendte SQL-syntaks:** Gemme forskellige JSON-dokumenter i DocumentDB og forespørge på disse dokumenter via en velkendte SQL-syntaks. DocumentDB anvender en meget samtidige Lås gratis, log strukturerede indeksering teknologi automatisk indeksere alt dokumentindhold. Dette gør det muligt for omfattende realtid forespørgsler uden at skulle angive skema tip, sekundære indeks eller visninger. Se mere i [Forespørgsel DocumentDB](documentdb-sql-query.md). 

-   **Udførelse af JavaScript i databasen:** Udtrykke programlogik som lagrede procedurer, udløsere og brugerdefinerede funktioner ved hjælp af standard JavaScript. Dette giver mulighed for dit programlogik til at betjene over data uden at bekymre dig om uoverensstemmelsen mellem programmet og databaseskemaet. DocumentDB indeholder fulde transaktions udførelse af JavaScript-programlogik direkte i af-databaseprogram. Den dybe integration af JavaScript muliggør udførelse af Indsæt, og Erstat, Slet og vælg handlinger fra et JavaScript-program som en isolerede transaktion. Se mere i [DocumentDB serversiden programmering](documentdb-programming.md).

-   **Tunable konsistens niveauer:** Vælg fra fire klart defineret konsistens niveauer for at opnå optimal forholdet mellem konsistens og ydeevne. DocumentDB tilbyder til forespørgsler og få handlinger, fire forskellige konsistens niveauer: stærke, afgrænset staleness, session, og eventuel. Disse findelt, klart defineret konsistens niveauer gør det muligt at foretage lyd kompromiser mellem konsistens, tilgængelighed og ventetid. Lær mere på [ved hjælp af konsistens niveauer for at maksimere tilgængelighed og ydeevne i DocumentDB](documentdb-consistency-levels.md).

-   **Fuldt administreret:** Undgå at skulle administrere database og maskine ressourcer. Som en fuldt administreret Microsoft Azure tjeneste, du ikke har brug for at administrere virtuelle maskiner, installere og konfigurere software, administrere skalering, eller håndtere komplekse data lag opgraderinger. Hver database er automatisk sikkerhedskopieres og beskyttet mod internationale mislykkede forsøg. Du kan nemt tilføje en DocumentDB konto og klargøre kapacitet, som du vil have det, så du kan fokusere på dit program i stedet for operativsystem og administrere din database. 

-   **Åbnet af design:** Komme hurtigt i gang ved hjælp af eksisterende viden og værktøjer. Programmering mod DocumentDB er enkel, approachable og kræver ikke muligt at indføre nye værktøjer eller overholde brugerdefinerede udvidelser til JSON eller JavaScript. Du kan få adgang til alle de Databasefunktioner, herunder CRUD, forespørgsel og JavaScript behandling over en enkel RESTful HTTP-grænseflade. DocumentDB inddraget eksisterende formater, sprog og standarder mens giver høj værdi Databasefunktioner oven på dem.

-   **Automatisk indeksering:** Som standard DocumentDB [automatisk indekserer](documentdb-indexing.md) alle dokumenter i databasen og ikke forventer eller kræver et skema eller oprettelse af sekundære indeks. Vil du indeksere alt ikke? Bare rolig, kan du [fravælge stier i filerne JSON](documentdb-indexing-policies.md) for.

##<a name="data-management"></a>Hvordan administrere DocumentDB data?

Azure DocumentDB håndterer JSON data via klart defineret databaseressourcer. Disse ressourcer replikeres til høj tilgængelighed og er sikret tilgængelige af deres logiske URI. DocumentDB indeholder en enkel HTTP baseret RESTful programming model for alle ressourcer. 

Kontoen DocumentDB database er et entydigt navneområde, der giver dig adgang til Azure DocumentDB. Før du kan oprette en database-konto, skal du have et Azure-abonnement, som giver dig adgang til en række Azure tjenester. 

Alle de ressourcer i DocumentDB er formateret og gemt som JSON dokumenter. Ressourcer administreres som elementer, som er JSON dokumenter, der indeholder metadata, og som feeds, der er samlinger af elementer. Sæt af elementer, befinder sig i deres respektive feeds.

Billedet nedenfor viser relationerne mellem DocumentDB ressourcerne:

![Hierarkisk relationen mellem ressourcer i DocumentDB, en NoSQL JSON-database][1] 

En database konto består af et sæt af databaser, hver med flere samlinger, hver især kan indeholde lagrede procedurer, udløsere, brugerdefinerede funktioner, dokumenter og relaterede vedhæftede filer. En database er også knyttet brugere, hver med et sæt af tilladelser til at få adgang til forskellige andre samlinger, lagrede procedurer, udløsere, brugerdefinerede funktioner, dokumenter eller vedhæftede filer. Mens databaser, brugere, tilladelser og samlinger er defineret af systemet ressourcer med kendte skemaer - dokumenter, lagrede procedurer, udløsere, brugerdefinerede funktioner og vedhæftede filer indeholde vilkårlig, brugerdefinerede JSON indhold.  

##<a name="develop"></a>Hvordan kan jeg udvikle apps med DocumentDB?

Azure DocumentDB Fremviser ressourcer via en REST-API, der kan kaldes af en hvilken som helst kan foretage HTTP/HTTPS-anmodninger sprog. Desuden tilbyder DocumentDB programming biblioteker for flere populære sprog. Disse biblioteker forenkle mange aspekter af arbejde med Azure DocumentDB ved at håndtere oplysninger såsom adresse cachelagring, undtagelse administration, automatisk forsøg og så videre. Biblioteker er tilgængelige for øjeblikket til følgende sprog og platforme:  

Download | Dokumentation
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET-bibliotek](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js bibliotek](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java-bibliotek](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript-bibliotek](http://azure.github.io/azure-documentdb-js/)
i/t. | [Serversiden JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python bibliotek](http://azure.github.io/azure-documentdb-python/)

Ud over grundlæggende oprette, læse, opdatere og slette handlinger, DocumentDB indeholder en omfattende SQL-forespørgsel grænseflade til hentning af JSON dokumenter og serversiden understøttelse af transaktions udførelse af JavaScript-programlogik. Udførelse af forespørgsel og script-grænseflader er tilgængelige via alle platform biblioteker, samt de REST API'er. 

### <a name="sql-query"></a>SQL-forespørgsel
Azure DocumentDB understøtter forespørgsler dokumenter ved hjælp af en SQL-sprog, der findes i JavaScript type system og udtryk med support for relationel, hierarkisk og geografiske forespørgsler. Forespørgselssprog DocumentDB er en enkle og effektive brugergrænseflade til forespørgsel JSON dokumenter. Sproget, der understøtter et undersæt af ANSI SQL-grammatik og tilføjer dybe integration af JavaScript objekt, matrixer, objekt byggeri og funktion aktivering. DocumentDB giver dens forespørgsel model uden en hvilken som helst eksplicitte skema eller indeksering tip fra udvikleren.

Brugerdefinerede funktioner (brugerdefinerede funktioner) kan være registreret hos DocumentDB og refereres til som en del af en SQL-forespørgsel og udvide grammatikkontrol for at understøtte brugerdefineret programlogik. Disse brugerdefinerede funktioner er skrevet som JavaScript-programmer og udføres i databasen. 

DocumentDB tilbyder også en udbyder af LINQ forespørgsel som en del af [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx)til .NET udviklere. 

### <a name="transactions-and-javascript-execution"></a>Transaktioner og udførelse af JavaScript
DocumentDB kan du skrive programlogik som navngivet programmer, der er skrevet helt på JavaScript. Disse programmer er registreret i en samling og udsender databasehandlinger på dokumenter i en bestemt samling. JavaScript kan registreres til udførelse af som udløser, lagrede procedure eller brugerdefineret funktion. Udløsere og lagrede procedurer kan oprette, læse, opdatere og slette dokumenter, mens udføre brugerdefinerede funktioner som en del af udførelse af forespørgsel logik uden at skrive-adgang til samlingen.

Udførelse af JavaScript i DocumentDB er udviklet efter de begreber, der understøttes af relationsdatabasesystemer med JavaScript som en moderne erstatning for Transact-SQL. Alle JavaScript-logik udføres inden for en omgivende SURT transaktion med øjebliksbillede isolationsniveauet. I løbet af kørslen, hvis JavaScript'et medfører en undtagelse afbrydes derefter hele posteringen.

## <a name="next-steps"></a>Næste trin
Allerede har en Azure-konto? Du kan derefter Introduktion til DocumentDB i [Azure-portalen](https://portal.azure.com/#gallery/Microsoft.DocumentDB) ved at [oprette en DocumentDB database-konto](documentdb-create-account.md).

Ikke har en Azure-konto? Du kan:

- Tilmelde dig en [gratis prøveversion af Azure](https://azure.microsoft.com/free/), som giver dig 30 dage og 200 kan du prøve de Azure tjenester. 
- Hvis du har et MSDN-abonnement, er du berettiget til [150 i gratis Azure kredit månedsbasis](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) til brug på en hvilken som helst Azure service. 

Derefter, når du er klar til at få mere at vide, skal du besøge vores [læringssti](https://azure.microsoft.com/documentation/learning-paths/documentdb/) for at gå alle de undervisningsressourcer, der er tilgængelige for dig. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
