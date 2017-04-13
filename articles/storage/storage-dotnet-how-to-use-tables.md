<properties
    pageTitle="Introduktion til Azure Table storage ved hjælp af .NET | Microsoft Azure"
    description="Gem strukturerede data i skyen ved hjælp af Azure Table storage, et NoSQL datalager."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-table-storage-using-net"></a>Introduktion til Azure Table storage ved hjælp af .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt

Azure-tabellager er en tjeneste, der gemmer strukturerede NoSQL data i skyen. Tabellagring er et lager til tasten/attribut med et schemaless design. Da Table storage er schemaless, er det nemt at tilpasse dine data som behovet i dit program evolve. Adgang til data er hurtigt og økonomisk for alle typer programmer. Tabellagring er typisk betydeligt lavere i omkostninger end traditionelle SQL for lignende datamængder.

Du kan bruge Table storage til at gemme fleksible datasæt, som brugerdata for webprogrammer, adressekartoteker, enhedsoplysninger og enhver anden type metadata, der kræver, at din tjeneste. Du kan gemme et vilkårligt antal objekter i en tabel, og en lagerplads konto kan indeholde et vilkårligt antal tabeller, op til kapacitetsgrænsen til kontoen, lagerplads.

### <a name="about-this-tutorial"></a>Om dette selvstudium

Dette selvstudium viser, hvordan du kan skrive .NET-koden for nogle almindelige scenarier, ved hjælp af Azure Table storage, herunder oprettelse og sletning af en tabel og indsætte, opdatere, slette og forespørgsler tabeldata.

**Anslået tid at gennemføre:** 45 minutter

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure-lager klientbibliotek til .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Konfigurationsstyring til .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- En [Azure lagerplads konto](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Flere eksempler

For flere eksempler på brug af tabellagring, kan du se [Introduktion til Azure Table Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Du kan hente eksempelprogrammet og køre den, eller du kan gennemse koden på GitHub.


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Tilføje erklæringer af navneområder

Tilføj følgende `using` sætninger til toppen af den `program.cs` filer:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>Fortolke forbindelsesstrengen

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Oprette tabel service-klienten

Klassen **CloudTableClient** gør det muligt at hente tabeller og objekter, der er gemt i Table storage. Her er en metode til at oprette service klienten:

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

Nu er du klar til at skrive programkode, der læser data fra og skriver data til Table storage.

## <a name="create-a-table"></a>Oprette en tabel

I dette eksempel viser, hvordan du opretter en tabel, hvis den ikke allerede findes:

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel

Knytte enheder til C\# objekter ved hjælp af en brugerdefineret klasse afledt **TableEntity**. Hvis du vil tilføje et objekt til en tabel, skal du oprette en klasse, der definerer egenskaberne for din enhed. Følgende kode definerer en enhed klasse, der bruger kundens fornavn som række nøgle og efternavn som tasten partition. Sammen, identificerer et objekt partition og række nøgle entydigt objektet i tabellen. Objekter med den samme Partitionsnøgle kan forespørges hurtigere end dem med forskellige partition taster, men med forskellige partition taster giver mulighed for større skalerbarhed af parallelle handlinger.  For en hvilken som helst egenskab, der skal være gemt i tjenesten tabel, egenskaben skal være en offentlig egenskab til en understøttet type, der viser begge `get` og `set`.
Desuden fremvise din enhed type *skal* en parameter mindre parametre.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Tabel handlinger, der involverer enheder udføres via det **CloudTable** objekt, du oprettede tidligere i afsnittet "Oprette en tabel". Handlingen udføres er repræsenteret af en **TableOperation** objekt.  Følgende kodeeksempel viser oprettelse af objektet **CloudTable** og derefter et **CustomerEntity** objekt.  For at forberede handlingen skal oprettes et **TableOperation** objekt for at indsætte objektet kunde i tabellen.  Til sidst skal er handlingen udført ved at ringe til **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Indsætte en gruppe objekter

Du kan indsætte en gruppe af objekter i en tabel i én skrivehandling. Nogle andre noter på batchen handlinger:

-  Du kan foretage opdateringer, slettes, og indsættes i den samme enkelte batchen operation.
-  En enkelt batchhandling kan medtage op til 100 enheder.
-  Alle objekter i en enkelt batchhandling skal have den samme Partitionsnøgle.
-  Selvom det er muligt at udføre en forespørgsel som en batchhandling, kan det være handlingen kun i batchen.

<!-- -->
Følgende kodeeksempel opretter to enhed objekter og tilføjer hvert til **TableBatchOperation** ved hjælp af metoden **Indsæt** . Derefter kaldes **CloudTable.Execute** for at udføre handlingen.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>Hente alle objekter i en partition

Hvis du vil forespørge en tabel for alle objekter i en partition, skal du bruge et **TableQuery** objekt.
Følgende kodeeksempel angiver et filter for enheder, hvor "Jensen" er tasten partition. I dette eksempel udskriver felterne for de enkelte objekter i forespørgselsresultaterne til konsollen.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Hente et interval af objekter i en partition

Hvis du ikke vil forespørge alle objekterne i en partition, kan du angive et område ved at kombinere filteret partition vigtige med en række vigtige filter. Følgende kodeeksempel bruger to filtre til at få alle objekter i partition "Jensen", hvor tasten række (Fornavn) starter med et bogstav, der er tidligere end 'E' i alfabetet og derefter udskrives forespørgselsresultaterne.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>Hente et enkelt objekt

Du kan skrive en forespørgsel for at hente en enkelt, specifik enhed. Følgende kode bruger **TableOperation** til at angive kunden 'Ben Smith'.
Denne metode returnerer et enkelt objekt i stedet for en samling, og den returnerede værdi i **TableResult.Result** er et **CustomerEntity** objekt.
Angive både partition og række taster i en forespørgsel er den hurtigste måde at hente et enkelt objekt fra tjenesten tabel.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>Erstatte et objekt

Hvis du vil opdatere et objekt, hente det fra tjenesten tabel, redigere objektet enhed og derefter gemme ændringerne tilbage til tjenesten tabel. Følgende kode ændres en eksisterende kunde telefonnummer. I stedet for opkald, **indsætte**, bruger denne kode **Erstat**. Derved objektet skal erstattes fuldt ud på serveren, medmindre objektet på serveren er ændret, siden det blev hentet, i hvilket tilfælde handlingen mislykkes.  Denne fejl er at forhindre, at dit program fra ved et uheld overskrive en ændring, der er foretaget mellem hentning og opdatering af en anden komponent i dit program.  Stort håndtering af denne fejl er at hente objektet igen, Foretag dine ændringer (hvis det er stadig er gyldig), og udfør derefter en anden **erstatte** handling.  Næste afsnit viser dig, hvordan at tilsidesætte denne funktionsmåde.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>Indsæt eller-Erstat et objekt

**Erstatte** handlinger mislykkes, hvis objektet er blevet ændret siden den blev hentet fra serveren.  Desuden skal du hente objektet fra serveren først i rækkefølge for handlingen **Erstat** kan lykkes.
Nogle gange, men ved du ikke, hvis objektet, der findes på serveren, og de aktuelle værdier, der er gemt i det er relevant. Opdateringen skal overskrive dem alle.  For at opnå dette, skal bruge du handlingen **InsertOrReplace** .  Denne handling indsætter enheden, hvis det ikke findes, eller erstatter den, hvis det er tilfældet, uanset hvor den seneste opdatering blev oprettet.  I kodeeksemplet nedenfor objektet kunde for Ben Smith hentes stadig, men gemmes det derefter tilbage til serveren via **InsertOrReplace**.  Opdateringer til objektet mellem hentning og Opdater handlinger vil blive overskrevet.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>Forespørgsel et undersæt af egenskaber for enheder

En tabeloprettelsesforespørgsel kan hente blot nogle få egenskaber fra et objekt i stedet for alle objektegenskaber. Denne metode, kaldet projicering, reducerer båndbredde og kan forbedre forespørgselsydeevne, især for store enheder. Forespørgslen i følgende kode returnerer kun mailadresser af enheder i tabellen. Det gør du ved hjælp af en forespørgsel for **DynamicTableEntity** samt **EntityResolver**. Du kan lære mere om projicering i [Introduktion til Upsert og forespørgsel projicering blog skrive][]. Bemærk, at projicering ikke understøttes på emulatoren lokale lager, så denne kode køres kun, når du bruger en konto på tjenesten tabel.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>Slette et objekt

Du kan nemt slette et objekt, når du har hentet, ved hjælp af det samme mønster, der vises for opdatering af et objekt.  Følgende kode henter og sletter en kunde-enhed.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>Slette en tabel

Følgende kodeeksempel sletter endelig, en tabel fra en lagerplads konto. En tabel, der er blevet slettet udgår skal oprettes igen til et stykke tid, efter sletningen.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>Hente enheder i sider asynkront

Hvis du læser et stort antal enheder, og du vil proces/Vis enheder, som de er blevet hentet i stedet for venter på dem alle til at returnere, kan du hente objekter ved hjælp af en segmenteret forespørgsel. I dette eksempel viser, hvordan til at returnere resultater i sider ved hjælp af mønstret asynkron – venter på at blive så udførelse af ikke blokeres, mens du venter for et stort antal resultater til at returnere. Du kan finde flere oplysninger om brugen af asynkrone Await mønster i .NET [asynkron programmering med asynkron og Await (C# og Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>Næste trin

Nu, hvor du har lært grundlæggende tabel lagerplads, skal du følge disse links for at lære mere komplekse lagerplads opgaver:

- Se flere eksempler på tabel lagerplads i [Introduktion til Azure Table Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- Få vist i tabellen service referencedokumentationen til alle oplysninger om tilgængelige API'er:
    - [Lagerplads klientbibliotek til .NET reference](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST-API reference](http://msdn.microsoft.com/library/azure/dd179355)
- Lær at forenkle den kode, du skriver til arbejde med Azure-lager ved hjælp af [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- Få vist flere funktion hjælpelinjer for at få mere at vide om yderligere indstillinger for lagring af data i Azure.
    - [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md) for at gemme ustrukturerede data.
    - [Opret forbindelse til SQL-Database ved hjælp af .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) til at gemme relationelle data.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Introduktion Upsert og forespørgsel projicering blogindlæg]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
