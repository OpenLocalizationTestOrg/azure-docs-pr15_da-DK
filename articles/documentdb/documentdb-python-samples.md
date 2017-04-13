<properties 
    pageTitle="Eksempler på NoSQL Python til DocumentDB | Microsoft Azure" 
    description="Find NoSQL Python eksempler på github til almindelige opgaver i DocumentDB, herunder CRUD-handlinger for JSON dokumenter i NoSQL databaser." 
    keywords="Python eksempler"
    services="documentdb" 
    authors="moderakh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter="python"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/18/2016" 
    ms.author="moderakh"/>


# <a name="documentdb-python-examples"></a>DocumentDB Python eksempler

> [AZURE.SELECTOR]
- [.NET eksempler](documentdb-dotnet-samples.md)
- [Node.js eksempler](documentdb-nodejs-samples.md)
- [Python eksempler](documentdb-python-samples.md)
- [Azure kode eksempel galleri](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Eksempel-løsninger, der udfører CRUD handlinger og andre almindelige handlinger på Azure DocumentDB ressourcer er inkluderet i [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) GitHub lager. I denne artikel indeholder:

- Links til opgaverne i hvert af Python eksempel projektfilerne. 
- Links til relaterede API referere til indhold.

**Forudsætninger**

1. Du skal bruge en Azure-konto for at bruge disse Python eksempler:
    - Du kan [åbne en Azure-konto gratis](https://azure.microsoft.com/pricing/free-trial/): du får kredit du kan bruge til at prøve betalt Azure tjenester, og selv efter at de er vant op du holder kontoen, og brug ledig Azure tjenester, som websteder. Dit kreditkort vil aldrig blive faktureret, medmindre du eksplicit ændre dine indstillinger og bede om at betale.
   - Du kan [aktivere Visual Studio abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): dit Visual Studio-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.
2. Du skal også [Python SDK](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Hver prøve er selvstændig, konfigurerer selve og rydder op efter sig selv. Som sådan Udsted eksemplerne flere opkald til [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Hver gang dette sker abonnementet vil blive faktureret for 1 time af brugen per ydeevne niveau i gruppen af websteder blive oprettet. 

## <a name="database-examples"></a>Database eksempler

Filen [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) projektets viser, hvordan du udføre følgende opgaver.

Opgave | API reference
--- | ---
[Oprette en database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Forespørgsel en konto til en database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document_client. QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Læse en database efter Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document_client. ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Liste over databaser for en konto](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Slette en database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document_client. DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## <a name="collection-examples"></a>Samling eksempler 

Filen [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) projektets viser, hvordan du udføre følgende opgaver.

Opgave | API reference
--- | ---
[Oprette en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Læse en liste over alle websteder i en database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document_client. ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Få en samling af-Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document_client. ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Få ydeevne niveau i en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ændre ydeevnen niveau i en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document_client. ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Slette en samling](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document_client. DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
