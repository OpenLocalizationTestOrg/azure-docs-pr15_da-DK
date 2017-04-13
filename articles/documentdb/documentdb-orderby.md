<properties 
    pageTitle="Sortering af DocumentDB data ved hjælp af Order By | Microsoft Azure" 
    description="Lær, hvordan du bruger ORDER BY i DocumentDB forespørgsler i LINQ og SQL, og hvordan du kan angive en indeksering politik for ORDER BY forespørgsler." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>Sortering af DocumentDB data ved hjælp af Order By
Microsoft Azure DocumentDB understøtter forespørgsel dokumenter ved hjælp af SQL over JSON dokumenter. Forespørgselsresultater kan bestilt ved hjælp af delsætningen ORDER BY i SQL-forespørgsel-sætninger.

Når du har læst i denne artikel, vil du kunne besvare spørgsmål, der er følgende: 

- Hvordan jeg forespørgsler med Order By?
- Hvordan konfigurerer jeg en politik for indeksering for Order By?
- Hvad der kommer næste?

[Eksempler](#samples) og en [ofte stillede spørgsmål om](#faq) er også angivet.

Du kan finde en komplet reference på SQL-forespørgsler, [DocumentDB forespørgsel selvstudium](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Sådan forespørgsel med rækkefølge efter
Som i ANSI-SQL, kan du nu medtage valgfrit delsætningen Order By i SQL-sætninger ved forespørgsler DocumentDB. Delsætningen kan indeholde et valgfrit ASC/DESC-argument for at angive den rækkefølge, hvori der skal hentes resultater. 

### <a name="ordering-using-sql"></a>Rækkefølge ved hjælp af SQL
Her er for eksempel en forespørgsel til at hente de 10 vigtigste bøger i faldende rækkefølge af deres titler. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Rækkefølge ved hjælp af SQL med filtrering
Du kan bestille ved hjælp af en hvilken som helst indlejrede egenskab i dokumenter som Books.ShippingDetails.Weight, og du kan angive ekstra filtre i WHERE-delsætningen sammen med Order By som i dette eksempel:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Rækkefølge ved hjælp af LINQ-Provider til .NET
Ved hjælp af .NET SDK version 1.2.0 og nyere kan du også kan bruge delsætningen OrderBy() eller OrderByDescending() i LINQ forespørgsler som i dette eksempel:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB understøtter rækkefølge med en enkelt numeriske, streng eller boolesk egenskab per-forespørgsel med flere forespørgselstyper kommer snart. Se [hvad der kommer næste](#Whats_coming_next) for at få flere oplysninger.

## <a name="configure-an-indexing-policy-for-order-by"></a>Konfigurere en politik for indeksering til Order By

Tilbagekalde, DocumentDB understøtter to typer af indeks (Hash og område), som kan angives til bestemte stier/egenskaber, datatyper (strenge/tal) og på forskellige præcisionsværdier (maksimal præcision eller en fast nøjagtighed værdi). Da DocumentDB bruger Hash indeksering som standard, skal du oprette en ny samling med en brugerdefineret indeksering politik med celleområde på tal, strenge eller begge for at kunne bruge Order By. 

>[AZURE.NOTE] Streng område indeks blev introduceret på 7 juli 2015 med REST-API version 2015-06-03. For at oprette politikker til Order By mod strenge, skal du bruge SDK version 1.2.0 af .NET SDK eller version 1.1.0 af Python, Node.js eller Java SDK.
>
>Før du REST-API version 2015-06-03 blev standardpolitikken samling indeksering Hash for både strenge og tal. Dette er blevet ændret til Hash til strenge og område for tal. 

Få flere oplysninger under [DocumentDB indeksering politikker](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Indeksering af Order By mod alle egenskaber
Her er hvordan du kan oprette en samling med "Alle interval" indeksering af Order By mod alle/alle numerisk eller streng egenskaber, der vises i JSON dokumenter i den. Her kan vi tilsidesætte indeks standardtypen for strengværdier til område, og på den maksimale præcision (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Bemærk, at Order By kun returnerer resultatet af de datatyper (strenge og tal), der er indekseret med en RangeIndex. Eksempelvis hvis du har standard indeksering politik, der kun indeholder RangeIndex på tal, returnerer en Order By mod en sti med strengværdier ingen dokumenter.

### <a name="indexing-for-order-by-for-a-single-property"></a>Indeksering af Order By for en enkelt egenskab
Her er, hvordan du kan oprette en samling med indeksering af Order By mod lige Titelegenskaben, som er en streng. Der er to stier, én for Titelegenskaben ("/ Titel /?") med område indeksering og et til alle de andre egenskaber med standard indeksering ordning, som er Hash til strenge og område for tal.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Eksempler
Se nærmere på denne [Github eksempler projekt](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) , der viser, hvordan du bruger Order By, herunder oprettelse af indeksering politikker og sideopdeling ved hjælp af Order By. Eksemplerne er Åbn kilde og opfordrer vi dig til at sende hente anmodninger med bidrag, der kan få glæde andre DocumentDB udviklere. Se [bidrag retningslinjer](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) for vejledning i at bidrage.  

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

**Hvad er det forventede anmode om enhed (RU) forbrug af forespørgsler Order By?**

Da anvender opslag DocumentDB indekset Order By, bliver antallet af anmodning om enheder, der benyttes af Order By forespørgsler svarer til de tilsvarende forespørgsler uden Order By. Som enhver anden proces på DocumentDB er antallet af anmodning om enheder afhænger af, at størrelser figurerne af dokumenter samt forespørgslens kompleksitet. 


**Hvad er den forventede indeksering spild af Order By?**

Indeksering lagerplads spild bliver proportionalt med antallet af egenskaber. I den værst tænkelige scenario bliver indeks spild 100% af dataene. Der er ingen forskel i overførselshastighed (anmode om enheder) omkostninger mellem område/Order By indeksering og standard Hash indeksering.

**Hvordan jeg forespørge min eksisterende data i DocumentDB ved hjælp af Order By?**

Hvis du vil sortere forespørgselsresultater ved hjælp af Order By, skal du ændre politikken indeksering af samlingen for at bruge en område Indekstype mod egenskaben bruges til at sortere. Se [ændre indeksering politik](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Hvad er begrænsninger i Order By?**

ORDER By kan angives kun mod en egenskab, enten numerisk eller streng, når det er område indekseret med den maksimale præcision (-1).

Du kan ikke udføre følgende:
 
- ORDER By med strengegenskaber for interne som-id, _rid og _self (kommer snart).
- Udledt fra resultatet af en handel dokument joinforbindelse (kommer snart) Order By med egenskaber.
- Sortér efter flere egenskaber (kommer snart).
- ORDER By med forespørgsler på databaser, samlinger, brugere, tilladelser eller vedhæftede filer (kommer snart).
- Sortér efter med beregnede egenskaber fx resultatet af et udtryk eller en UDF/indbyggede-i funktion.

ORDER By understøttes ikke i øjeblikket for forespørgsler i tværs partition ved brug af Explorer forespørgsel i portalen Azure.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du modtager en fejl, Order By ikke understøttes, skal du kontrollere for at sikre, at du bruger en version af [SDK](documentdb-sdk-dotnet.md) , som understøtter Order By. 

## <a name="next-steps"></a>Næste trin

Deler sig [Github eksempler projektet](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) og starte bestilling af dine data! 

## <a name="references"></a>Referencer
* [DocumentDB forespørgsel Reference](documentdb-sql-query.md)
* [DocumentDB indeksering politikreference](documentdb-indexing-policies.md)
* [DocumentDB SQL-Reference](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [DocumentDB rækkefølge efter eksempler](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

