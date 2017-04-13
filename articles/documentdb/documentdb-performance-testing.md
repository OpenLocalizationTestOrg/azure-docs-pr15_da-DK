<properties 
    pageTitle="DocumentDB skala og test af ydeevne | Microsoft Azure" 
    description="Lær, hvordan du udfører skala og performance-test med Azure DocumentDB"
    keywords="Test af ydeevne"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Ydeevne og skalering test med Azure DocumentDB
Skala test af ydeevne og er et vigtigt skridt i udvikling af programmer. For mange programmer, kan databasen niveauet har stor indflydelse på den overordnede ydeevne og skalerbarhed og kan derfor en vigtig komponent i test af ydeevne. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) er specialudviklede for elastiske skala og mere forudsigelige ydeevne, og derfor velegnet til programmer, der har brug for en høj ydeevne databaseniveau. 

I denne artikel er en reference til udviklere implementere ydeevne test-pakker til deres DocumentDB arbejdsmængder eller evaluering af DocumentDB for scenarier med høj ydeevne programmet. Den fokuserer primært på test af isolerede ydeevne af databasen, men også indeholder bedste praksis for fremstilling programmer.

Når du har læst i denne artikel, vil du kunne besvare spørgsmål, der er følgende:   

- Hvor kan jeg finde et eksempel .NET-klientprogrammet til test af ydeevne af Azure DocumentDB? 
- Hvordan opnå høj overførselshastighed niveauer med Azure DocumentDB fra min klientprogrammet?

For at komme i gang med kode, kan du hente projektet fra [DocumentDB ydeevne test eksempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] Formålet med dette program er at dokumentere bedste fremgangsmåder til at udtrække bedre ydeevne ud af DocumentDB med et lille antal klientcomputere. Dette ikke er gjort til at vise spidsbelastning kapaciteten af den tjeneste, som kan skalere limitlessly.

Hvis du vil have klientsiden konfigurationsindstillinger DocumentDB ydeevnen, skal du se [tip om DocumentDB ydeevne](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Køre ydeevnen tester program
Den hurtigste måde at komme i gang er at samle og køre .NET eksemplet nedenfor, som beskrevet i nedenstående fremgangsmåde. Du kan også gennemse koden og implementere lignende konfigurationer til din egen klientprogrammer.

**Trin 1:** Hent projektet fra [DocumentDB ydeevne test eksempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), eller deler sig Github-lager.

**Trin 2:** Ændre indstillingerne for EndpointUrl, AuthorizationKey, CollectionThroughput og DocumentTemplate (valgfrit) i App.config.

> [AZURE.NOTE] Før klargøring af websteder med høj overførselshastighed kan se [Priser side](https://azure.microsoft.com/pricing/details/documentdb/) for at anslå omkostninger per af websteder. DocumentDB fakturaer lager og overførselshastighed enkeltvis på hver time basis, så du kan spare udgifter ved at slette eller at sænke hastigheden af DocumentDB samlinger efter testen.

**Trin 3:** Samle og køre console-app fra kommandolinjen. Du burde se output ud som følger:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Trin 4 (hvis det er nødvendigt):** Dataoverførsel rapporteret (RU/s) fra værktøjet skal være den samme eller højere end den klargjorte overførsel af gruppen af websteder. Hvis ikke, øge DegreeOfParallelism i små ryk kan hjælpe dig med at træffe grænsen. Hvis overførselshastighed fra din klient app plateauer hjælper at åbne flere forekomster af appen på de samme eller forskellige computere dig med at træffe klargjort grænsen på tværs af forskellige forekomster. Hvis du har brug for hjælp med dette trin, skal du skrive en mail til askdocdb@microsoft.com eller udfylde en supportbilletter.

Når du har den app, der kører, kan du prøve forskellige [indeksering politikker](documentdb-indexing-policies.md) og [konsistens niveauer](documentdb-consistency-levels.md) for at forstå deres påvirkning af hastighed og ventetid. Du kan også gennemse koden og implementere lignende konfigurationer til din egen test-programpakker eller -fremstilling programmer.

## <a name="next-steps"></a>Næste trin
I denne artikel, har vi set hvordan du kan udføre ydeevne og skalering test med DocumentDB ved hjælp af en .NET console-app. Se linkene nedenfor for yderligere oplysninger om at arbejde med DocumentDB.

* [DocumentDB ydeevne test eksempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Klient konfigurationsindstillinger til at forbedre DocumentDB ydeevne](documentdb-performance-tips.md)
* [Serversiden partitionering i DocumentDB](documentdb-partition-data.md)
* [DocumentDB af websteder og ydeevne](documentdb-performance-levels.md)
* [DocumentDB .NET SDK dokumentation på MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET eksempler](https://github.com/Azure/azure-documentdb-net)
* [DocumentDB blog på tip om ydeevne](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
