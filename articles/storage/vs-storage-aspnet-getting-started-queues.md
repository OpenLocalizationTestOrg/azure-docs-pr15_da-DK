<properties
    pageTitle="Introduktion til kø lager og Visual Studio forbundne tjenester (ASP.NET) | Microsoft Azure"
    description="Sådan Introduktion til brug af Azure kø lager i en ASP.NET-projekt i Visual Studio, når forbindelsen til en lagerplads konto ved hjælp af Visual Studio forbundne tjenester"
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
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Introduktion til Azure kø lager og Visual Studio forbundne tjenester

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt

Denne artikel beskrives, hvordan Introduktion til brug af Azure kø lagerplads i Visual Studio, når du har oprettet eller refererer til en Azure lagerplads konto i en ASP.NET-projekt ved hjælp af dialogboksen Visual Studio **Tilføje forbundne tjenester** .

Vi viser, hvordan du opretter og få adgang til en Azure kø i kontoen lagerplads. Vi også viser dig hvordan du udfører handlinger i grundlæggende kø, som tilføjelse, redigering, læsning og fjerne beskeder i kø. Eksemplerne er skrevet i C#-kode og bruge [Microsoft Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Du kan finde flere oplysninger om ASP.NET, [ASP.NET](http://www.asp.net).

Azure kø lagerplads er en tjeneste til lagring af stort antal meddelelser, der kan åbnes fra et vilkårligt sted i verden via godkendte opkald ved hjælp af HTTP eller HTTPS. En enkelt kø besked kan bestå af op til 64 KB i størrelse, og en kø kan indeholde millioner af meddelelser, op til den samlede kapacitet grænse for en lagerplads konto.

## <a name="access-queues-in-code"></a>Access køer i kode

For at få adgang til køer i ASP.NET projekter, skal du medtage følgende elementer til en hvilken som helst C#-kildefilen, få adgang til Azure kø lagerplads.

1. Kontrollér, at erklæringerne af navneområder øverst i filen C# omfatter disse **ved hjælp af** sætninger.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Få et **CloudStorageAccount** objekt, der repræsenterer kontooplysningerne lagerplads. Brug følgende kode til at få den din lagerplads forbindelsesstreng og lagerplads kontooplysninger fra Azure service konfigurationen.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Få et **CloudQueueClient** objekt til at referere til kø objekterne i kontoen lagerplads.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Få et **CloudQueue** objekt til at referere til en bestemt kø.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Bemærk!** Bruge alle ovenstående kode foran koden i følgende eksempler.

## <a name="create-a-queue-in-code"></a>Oprette en kø i kode

For at oprette en Azure kø i kode, du kun tilføje et opkald til **CreateIfNotExists** til ovenstående kode.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Tilføje en meddelelse til en kø

For at indsætte en meddelelse i en eksisterende kø, skal du oprette et nyt **CloudQueueMessage** -objekt, og derefter kalde metoden **AddMessage** .

Et **CloudQueueMessage** objekt kan oprettes fra en streng (i formatet UTF-8) eller en bytematrix af.

Her er et eksempel, som indsætter meddelelsen 'Hej, store verden'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Læse en meddelelse i en kø

Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe til metoden PeekMessage().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Læse og fjerne en meddelelse i en kø

Din kode kan fjerne (deaktivere kø) en meddelelse fra en kø i to trin.
1. Ring til GetMessage() for at få den næste meddelelse i en kø. En meddelelse, der returneres fra GetMessage() ikke er synligt for andre kode, der læser meddelelser fra denne kø. Som standard forbliver denne meddelelse usynlige i 30 sekunder.
2.  For at afslutte fjerner meddelelsen fra køen skal du ringe til **DeleteMessage**.

Denne proces for at fjerne en meddelelse sikrer, at hvis din kode ikke behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Følgende kode kalder **DeleteMessage** højre, når meddelelsen er blevet behandlet.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Brug yderligere indstillinger for meddelelser i deaktivere kø

Der er to måder, du kan tilpasse hentning af meddelelse fra en kø.
Først, kan du få en mængde meddelelser (op til 32). Andet, kan du angive timeout for en længere eller kortere invisibility, så din kode, mere eller mindre tid til at behandle fuldt hver meddelelse. Følgende kodeeksempel bruger **GetMessages** metoden til at få 20 meddelelser i et opkald. Det behandler derefter hver meddelelse ved hjælp af en **foreach** løkke. Invisibility timeouten angives også til fem minutter for hver meddelelse. Bemærk, at 5 minutter starter for alle meddelelser på samme tid, så når 5 minutter er gået, siden opkaldet til **GetMessages**, de meddelelser, som ikke er blevet slettet bliver synlige igen.

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

Du kan få en estimering af antallet af beskeder i en kø. Metoden **FetchAttributes** spørger queueservice til at hente de kø attributter, herunder antallet meddelelser. Egenskaben **ApproximateMethodCount** returnerer den sidste værdi, der bliver hentet af metoden **FetchAttributes** uden at kalde queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Bruge asynkron-venter på at blive mønster med almindelige queueAPIs

I dette eksempel vises, hvordan du bruger det asynkron-venter på at blive mønster med almindelige queueAPIs. Eksemplet kalder asynkron version af hver af de angivne metoder, dette kan ses af asynkrone efter fix for hver enkelt metode. Når en asynkrone metode benyttes asynkron-venter på at blive mønster afbryder lokal udførelse, indtil opkaldet er fuldført. Dette problem gør det muligt for den aktuelle tråd til at udføre andre arbejde, som hjælper med at undgå flaskehalse for performance og forbedrer overordnede svartid dit program. Få mere at vide om brug af asynkrone Await mønster i .NET se [asynkron og Await (C# og Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Slette en kø

Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal ringe til metoden **slette** på objektet kø.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]