<properties
    pageTitle="Komme i gang med tabellagring og Visual Studio forbundne tjenester (ASP.NET) | Microsoft Azure"
    description="Sådan Introduktion til brug af Azure Table storage i et ASP.NET-projekt i Visual Studio, når forbindelsen til en lagerplads konto ved hjælp af Visual Studio forbundne tjenester"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-table-storage-and-visual-studio-connected-services-aspnet"></a>Komme i gang med tabellagring og Visual Studio forbundne tjenester (ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt
Denne artikel beskrives, hvordan Introduktion til brug af Azure Table storage i Visual Studio, når du har oprettet eller refererer til en Azure lagerplads konto i en ASP.NET-projekt ved hjælp af dialogboksen Visual Studio **Tilføje forbundne tjenester** . I denne artikel beskrives, hvordan til at udføre almindelige opgaver i Azure-tabeller, herunder oprettelse og sletning af en tabel, samt arbejde med tabel enheder. Eksemplerne er skrevet i C\# kode og bruge [Microsoft Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Mere generelle oplysninger om brug af Azure tabellen lagerplads, skal du se [Introduktion til Azure Table storage ved hjælp af .NET](storage-dotnet-how-to-use-tables.md).

Azure-tabellager gør det muligt at gemme store mængder strukturerede data. Tjenesten er en NoSQL datastore, der accepterer godkendte opkald fra i og uden for Azure skyen. Azure tabeller er ideel til lagring af strukturerede, ikke-relationelle data.


## <a name="access-tables-in-code"></a>Access-tabeller i kode

1. Kontrollér, at erklæringerne af navneområder øverst i filen C# omfatter disse **ved hjælp af** sætninger.

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Få et **CloudStorageAccount** objekt, der repræsenterer kontooplysningerne lagerplads. Brug følgende kode til at få den din lagerplads forbindelsesstreng og lagerplads kontooplysninger fra Azure service konfigurationen.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **NOTE** - bruge alle ovenstående kode foran koden i følgende eksempler.

3. Få et **CloudTableClient** objekt til at referere til tabellen objekterne i kontoen lagerplads.  

        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Få et objekt til **CloudTable** reference til at referere til en bestemt tabel og enheder.

        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Oprette en tabel i kode

For at oprette tabellen Azure skal kun tilføje et opkald til **CreateIfNotExistsAsync()** til den forrige kode.

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel

Hvis du vil tilføje et objekt til en tabel kan du oprette en klasse, der definerer egenskaberne for din enhed. Følgende kode definerer en enhed klasse, kaldet **CustomerEntity** , der bruger kundens fornavn som række nøgle og efternavn som tasten partition.

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

Tabel handlinger, der involverer enheder er udført ved hjælp af **CloudTable** objekt du oprettede tidligere i "Access-tabeller i kode". Objektet **TableOperation** repræsenterer operation, der skal udføres. Følgende kodeeksempel viser, hvordan du opretter en **CloudTable** objekt og et **CustomerEntity** objekt. For at forberede handlingen skal oprettes en **TableOperation** for at indsætte objektet kunde i tabellen. Til sidst skal er handlingen udført ved at ringe til CloudTable.ExecuteAsync.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Indsætte en gruppe objekter

Du kan indsætte flere enheder i en tabel i en enkelt skrivehandling. Følgende kodeeksempel opretter to enhed objekter ("Jeff Smith" og "Ben Smith"), føjer dem til et **TableBatchOperation** objekt ved hjælp af metoden Indsæt og derefter starter handlingen ved at ringe til **CloudTable.ExecuteBatchAsync**.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Få alle enheder i en partition
Hvis du vil forespørge en tabel for alle objekterne i en partition, skal du bruge et **TableQuery** objekt. Følgende kodeeksempel angiver et filter for enheder, hvor "Jensen" er tasten partition. I dette eksempel udskriver felterne for de enkelte objekter i forespørgselsresultaterne til konsollen.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## <a name="get-a-single-entity"></a>Få et enkelt objekt
Du kan skrive en forespørgsel for at få en enkelt, specifik enhed. Følgende kode bruger et **TableOperation** objekt til at angive en kunde med navnet 'Ben Smith'. Denne metode returnerer et enkelt objekt i stedet for en samling, og den returnerede værdi i **TableResult.Result** er et **CustomerEntity** objekt. Angive både partition og række taster i en forespørgsel er den hurtigste måde at hente et enkelt objekt fra tabeltjenesten.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
    
    // Print the phone number of the result.
    if (retrievedResult.Result != null)
        Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Slette et objekt
Du kan slette et objekt, når du har fundet den. Følgende kode søger efter en kunde-enhed, der hedder "Ben Smith", og hvis der er angivet, slettes den.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
