<properties
    pageTitle="Introduktion til Azure kø lagerplads ved hjælp af .NET | Microsoft Azure"
    description="Azure køer giver pålidelig, asynkron messaging mellem programkomponenter. I skyen SMS gør det muligt for dine programkomponenter skalere uafhængigt af hinanden."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Introduktion til Azure kø lagerplads ved hjælp af .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt

Azure kø lagerplads indeholder skyen messaging mellem programkomponenter. Designe programmer til skala, Afkoblet programkomponenter ofte, så de kan skalere uafhængigt af hinanden. Kø lagerplads leverer asynkron messaging af kommunikation mellem programkomponenter, uanset om de kører i skyen, på skrivebordet, på en lokal server eller på en mobilenhed. Kø lagerplads understøtter også administration af asynkrone opgaver og opbygning af processen arbejdsgange.

### <a name="about-this-tutorial"></a>Om dette selvstudium

Dette selvstudium viser, hvordan du kan skrive .NET-koden for nogle almindelige scenarier, ved hjælp af Azure kø lagerplads. Scenarier, der er dækket omfatter oprette og slette køer og tilføje, læse og slette beskeder i kø.

**Anslået tid at gennemføre:** 45 minutter

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure-lager klientbibliotek til .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Konfigurationsstyring til .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- En [Azure lagerplads konto](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Tilføje erklæringer af navneområder

Tilføj følgende `using` sætninger til toppen af den `program.cs` fil:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Fortolke forbindelsesstrengen

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Oprette kø service-klienten

Klassen **CloudQueueClient** gør det muligt at hente køer, der er gemt i kø lagerplads. Her er en metode til at oprette service klienten:

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Nu er du klar til at skrive programkode, der læser data fra og skriver data til kø lagerplads.

## <a name="create-a-queue"></a>Oprette en kø

I dette eksempel viser, hvordan du opretter en kø, hvis den ikke allerede findes:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Indsætte en meddelelse i en kø

Hvis du vil indsætte en meddelelse i en eksisterende kø, skal du først oprette en ny **CloudQueueMessage**. Derefter skal du ringe til metoden **AddMessage** . Du kan oprette en **CloudQueueMessage** fra en streng (i formatet UTF-8) eller en matrix af **byte** . Her er kode, som opretter en kø (hvis den ikke findes) og indsætter meddelelsen 'Hej, store verden':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Kig på den næste meddelelse

Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe til metoden **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Ændre indholdet af en meddelelse i køen

Du kan ændre indholdet af en meddelelse direkte i køen. Hvis meddelelsen repræsenterer en arbejdsopgave, kan du bruge denne funktion til at opdatere status for arbejdsopgaven. Følgende kode opdaterer kø meddelelsen med ny oplysningerne, og angiver synlighed timeout for at udvide en anden 60 sekunder. Dette gemmer tilstanden for arbejde, der er knyttet til meddelelsen, og giver klienten på en anden minut for at fortsætte med at arbejde i meddelelsesboksen. Du kan bruge denne teknik til at spore flere trin arbejdsprocesser på kø meddelelser uden at starte forfra fra begyndelsen, hvis et behandlingstrin mislykkes på grund af hardware og software fejl. Typisk, du vil beholde antal et forsøg samt, og hvis meddelelsen er mere end *n* gange forsøgt igen, skal du slette den. Dette beskytter mod en meddelelse, der udløser en programfejl, hver gang behandles.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>Deaktivere kø den næste meddelelse

Din kode kø deaktivere en meddelelse fra en kø i to trin. Når du ringer **GetMessage**, får du den næste meddelelse i en kø. En meddelelse, der returneres fra **GetMessage** ikke er synligt for andre kode, der læser meddelelser fra denne kø. Som standard forbliver denne meddelelse usynlige i 30 sekunder. For at afslutte fjerner meddelelsen fra køen, skal du også kontakte **DeleteMessage**. Denne proces for at fjerne en meddelelse sikrer, at hvis din kode ikke behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Din kode kalder **DeleteMessage** højre, når meddelelsen er blevet behandlet.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Bruge asynkron-venter på at blive mønster med fælles kø lager API'er

I dette eksempel vises, hvordan du bruger det asynkron-venter på at blive mønster med fælles kø lager API'er. Eksemplet kalder den asynkrone version af hver af de angivne metoder, som angivet af *asynkrone* suffikset i hver enkelt metode. Når en asynkrone metode benyttes, asynkron-venter på at blive mønster afbryder lokal udførelse, indtil opkaldet er fuldført. Dette problem gør det muligt for den aktuelle tråd til at udføre andre arbejde, som hjælper med at undgå flaskehalse for performance og forbedrer overordnede svartid dit program. Få mere at vide om brug af asynkrone Await mønster i .NET se [asynkron og Await (C# og Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Udnytte yderligere indstillinger for meddelelser i deaktivere kø

Der er to måder, du kan tilpasse hentning af meddelelse fra en kø.
Først, kan du få en mængde meddelelser (op til 32). Andet, kan du angive timeout for en længere eller kortere invisibility, så din kode, mere eller mindre tid til at behandle fuldt hver meddelelse. Følgende kodeeksempel bruger **GetMessages** metoden til at få 20 meddelelser i et opkald. Det behandler derefter hver meddelelse ved hjælp af en **foreach** løkke. Invisibility timeouten angives også til fem minutter for hver meddelelse. Bemærk, at 5 minutter starter for alle meddelelser på samme tid, så når 5 minutter er gået, siden opkaldet til **GetMessages**, de meddelelser, som ikke er blevet slettet bliver synlige igen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Få længde kø

Du kan få en estimering af antallet af beskeder i en kø. Metoden **FetchAttributes** beder tjenesten kø til at hente de kø attributter, herunder antallet meddelelser. Egenskaben **ApproximateMessageCount** returnerer den sidste værdi, der bliver hentet af metoden **FetchAttributes** uden at kalde tjenesten kø.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Slette en kø

Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal ringe til metoden **slette** på objektet kø.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende kø lagerplads, skal du følge disse links for at lære mere komplekse lagerplads opgaver.

- Få vist kø service referencedokumentation for alle oplysninger om tilgængelige API'er:
    - [Lagerplads klientbibliotek til .NET reference](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST-API reference](http://msdn.microsoft.com/library/azure/dd179355)
- Lær at forenkle den kode, du skriver til arbejde med Azure-lager ved hjælp af [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Få vist flere funktion hjælpelinjer for at få mere at vide om yderligere indstillinger for lagring af data i Azure.
    - [Introduktion til Azure Table storage ved hjælp af .NET](storage-dotnet-how-to-use-tables.md) for at gemme strukturerede data.
    - [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md) for at gemme ustrukturerede data.
    - [Opret forbindelse til SQL-Database ved hjælp af .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) til at gemme relationelle data.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
