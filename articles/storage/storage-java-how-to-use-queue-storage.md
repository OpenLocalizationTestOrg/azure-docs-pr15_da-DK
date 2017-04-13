<properties
    pageTitle="Sådan bruger du kø lagerplads fra Java | Microsoft Azure"
    description="Lær, hvordan du bruger tjenesten Azure kø til at oprette og slette køer, og Indsæt, hente og slette meddelelser. Eksempler, der er skrevet i Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Sådan bruger du kø lagerplads fra Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser dig, hvordan til at udføre almindelige scenarier ved hjælp af tjenesten Azure kø lagerplads. Eksemplerne er skrevet i Java og bruge [Azure lagerplads SDK til Java][]. De scenarier, der er omfattet omfatter **indsættelse**, **visning**, **hente**og **slette** beskeder i kø samt **oprettelse** og **sletning af** køer. Du kan finde flere oplysninger om køer, afsnittet [Næste trin](#Next-Steps) .

Bemærk: Et SDK er tilgængelig for udviklere, der bruger Azure-lager på Android-enheder. Se [Azure lagerplads SDK til Android][]kan finde flere oplysninger.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Oprette en Java-program

I denne vejledning, skal bruge du lagerplads funktioner, der kan afspilles i et Java-program, lokalt eller i kode, der kører i en web rolle eller arbejder rolle i Azure.

Hvis du vil gøre det, skal du installere Java Development Kit (JDK) og oprette en Azure-lager-konto i abonnementet Azure. Når du har gjort det, skal du bekræfte, at udviklingssystemet opfylder minimumkravene og afhængigheder, som er angivet i [Azure lagerplads SDK til Java][] -lager på GitHub. Hvis systemet opfylder kravene, kan du følge vejledningen for at hente og installere Azure Storage-biblioteker til Java på din computer fra lageret. Når du har udført disse opgaver, vil du kunne oprette en Java-program, som bruger eksemplerne i denne artikel.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurere dit program tilladelse til at få adgang til kø lagerplads

Tilføj følgende Importér udtalelser til toppen af filen Java, hvor du vil bruge Azure storage API'er til at få adgang til køer:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>Konfiguration af en forbindelsesstreng Azure-lager

En Azure-lager-klient bruges en forbindelsesstreng lager til lagring slutpunkter og legitimationsoplysninger for at få adgang til data management services. Når du kører i et klienten på computeren, skal du angive forbindelsesstrengen lagerplads i følgende format med navnet på kontoen lager og den primære hurtigtast til kontoen lagerplads, der er angivet i [Azure Portal](https://portal.azure.com) for værdierne *kontonavn* og *AccountKey* . I dette eksempel vises, hvordan du kan definere et statisk felt til at holde forbindelsesstrengen:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

I et program, der kører inden for en rolle i Microsoft Azure, denne streng kan være gemt i tjenesten konfigurationsfil *ServiceConfiguration.cscfg*, og kan åbnes med et opkald til metoden **RoleEnvironment.getConfigurationSettings** . Her er et eksempel for at få forbindelsesstrengen fra et **indstilling** element med navnet *StorageConnectionString* i konfigurationsfil service:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

I følgende eksempler forudsætter, at du har brugt en af disse to metoder til at få vist forbindelsesstreng lagerplads.

## <a name="how-to-create-a-queue"></a>Sådan: oprette en kø

Et **CloudQueueClient** objekt kan du få reference objekter til køer. Følgende kode opretter et **CloudQueueClient** objekt. (Bemærk: der er flere måder at oprette **CloudStorageAccount** objekter, kan finde flere oplysninger under **CloudStorageAccount** i [Referencen til Azure lagerplads Client SDK].)

Bruge objektet **CloudQueueClient** til at få en reference til den kø, du vil bruge. Du kan oprette køen, hvis den ikke findes.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Sådan: tilføje en meddelelse til en kø

Hvis du vil indsætte en meddelelse i en eksisterende kø, skal du først oprette en ny **CloudQueueMessage**. Derefter skal du ringe til metoden **addMessage** . Du kan oprette en **CloudQueueMessage** fra en streng (i formatet UTF-8) eller en bytematrix af. Her er kode, som opretter en kø (hvis den ikke findes) og indsætter meddelelsen "Hej, store verden".

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Sådan: Kig på den næste meddelelse

Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe til **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Sådan: ændre indholdet af en meddelelse i køen

Du kan ændre indholdet af en meddelelse direkte i køen. Hvis meddelelsen repræsenterer en arbejdsopgave, kan du bruge denne funktion til at opdatere status for arbejdsopgaven. Følgende kode opdaterer kø meddelelsen med ny oplysningerne, og angiver synlighed timeout for at udvide en anden 60 sekunder. Dette gemmer tilstanden for arbejde, der er knyttet til meddelelsen, og giver klienten på en anden minut for at fortsætte med at arbejde i meddelelsesboksen. Du kan bruge denne teknik til at spore flere trin arbejdsprocesser på kø meddelelser uden at starte forfra fra begyndelsen, hvis et behandlingstrin mislykkes på grund af hardware og software fejl. Typisk, du vil beholde antal et forsøg samt, og hvis meddelelsen er mere end *n* gange forsøgt igen, skal du slette den. Dette beskytter mod en meddelelse, der udløser en programfejl, hver gang behandles.

I følgende kode eksempel søges gennem køen af meddelelser, finder den første meddelelse, der svarer til "Hej, store verden" indholdet, og derefter ændrer meddelelsen indhold og afsluttet.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Du kan også opdaterer følgende eksempel-kode kun den første synlige meddelelse i køen.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Sådan: få længde kø

Du kan få en estimering af antallet af beskeder i en kø. Metoden **downloadAttributes** beder tjenesten kø for flere aktuelle værdier, herunder antal hvor mange meddelelser er i en kø. Antallet er kun tilnærmet, fordi meddelelser kan tilføjes eller fjernes, når tjenesten kø reagerer på din anmodning. Metoden **getApproximateMessageCount** returnerer den sidste værdi, der er hentet ved et opkald til **downloadAttributes**, uden at kalde tjenesten kø.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Sådan: annullering i kø den næste meddelelse

Din kode dequeues en meddelelse fra en kø i to trin. Når du ringer **retrieveMessage**, får du den næste meddelelse i en kø. En meddelelse, der returneres fra **retrieveMessage** ikke er synligt for andre kode, der læser meddelelser fra denne kø. Som standard forbliver denne meddelelse usynlige i 30 sekunder. For at afslutte fjerner meddelelsen fra køen, skal du også kontakte **deleteMessage**. Denne proces for at fjerne en meddelelse sikrer, at hvis din kode ikke behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Din kode kalder **deleteMessage** højre, når meddelelsen er blevet behandlet.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Yderligere indstillinger for Sådan flytning meddelelser

Der er to måder, du kan tilpasse hentning af meddelelse fra en kø. Først, kan du få en mængde meddelelser (op til 32). Andet, kan du angive timeout for en længere eller kortere invisibility, så din kode, mere eller mindre tid til at behandle fuldt hver meddelelse.

Følgende kodeeksempel bruger **retrieveMessages** metoden til at få 20 meddelelser i et opkald. Det behandler derefter hver meddelelse ved hjælp af en **til** en løkke. Invisibility timeouten angives også til fem minutter (300 sekunder) for hver meddelelse. Bemærk, at fem minutterne starter for alle meddelelser på samme tid, så når fem minutter er gået siden opkaldet til **retrieveMessages**, de meddelelser, som ikke er blevet slettet bliver synlige igen.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Sådan: listen køerne

For at få en liste over de aktuelle køer skal du ringe til metoden **CloudQueueClient.listQueues()** , som returnerer en samling af **CloudQueue** objekter.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Sådan: slette en kø

Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal du ringe metoden **deleteIfExists** på objektet **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende kø lagerplads, skal du følge disse links for at lære mere komplekse lagerplads opgaver.

- [Azure-lager SDK til Java][]
- [Azure-lager klient SDK Reference][]
- [Azure-lager Services REST-API][]
- [Azure-lager-teamets Blog][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure-lager SDK til Java]: https://github.com/azure/azure-storage-java
[Azure-lager SDK til Android]: https://github.com/azure/azure-storage-android
[Azure-lager klient SDK Reference]: http://dl.windowsazure.com/storage/javadoc/
[Azure-lager Services REST-API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure-lager-teamets Blog]: http://blogs.msdn.com/b/windowsazurestorage/
