<properties
    pageTitle="Behandle IoT Hub enhed i skyen meddelelser (Java) | Microsoft Azure"
    description="Følg dette Java selvstudium for at få mere at vide nyttige mønstre for at behandle IoT Hub enhed i skyen meddelelser."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>Selvstudium: Sådan behandles IoT Hub enhed i skyen meddelelser ved hjælp af Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der gør det muligt for pålidelig og sikker tovejs-kommunikation mellem millioner af IoT enheder og et program igen slutningen. Andre selvstudier ([komme i gang med IoT Hub] og [sende cloud-til-enhed meddelelser med IoT Hub][lnk-c2d]) viser, hvordan du den grundlæggende enhed i skyen og skyen-til-enhed SMS bruges af IoT Hub.

Dette selvstudium bygger på den kode, der vises i [Introduktion til IoT Hub] selvstudiet, og der vises to SVG mønstre, som du kan bruge til at behandle enhed i skyen meddelelser:

- Pålidelige opbevaring af enhed i skyen meddelelser i [Azure blob-lager]. Et almindelige scenarie er *kolde sti* analytics, hvor du gemmer telemetridata i BLOB skal bruges som input til analytics processer. Disse processer kan styres af funktioner som [Azure Data Factory] eller [HDInsight (Hadoop)] stablen.

- Pålidelige behandling af *interaktive* enhed i skyen meddelelser. Enhed i skyen meddelelser er interaktive, når de er øjeblikkelig udløsere til et sæt handlinger i programmet back-end. En enhed kan for eksempel sender en alarm, der udløser indsætning af en brugertilladelse i et CRM-system. Derimod feed *- datapunkt* meddelelser blot i en analytics-program. Temperaturen telemetri fra en enhed, der skal gemmes til senere analyse er for eksempel en datapunkt meddelelse.

Da IoT Hub Fremviser en [Begivenhed Hub][lnk-event-hubs]-kompatible slutpunkt, der skal modtage meddelelser enhed i skyen, dette selvstudium bruger en [EventProcessorHost] forekomst. Denne forekomst:

* Pålideligt indeholder *datapunkt* meddelelser i Azure blob-lager.
* Videresender *interaktive* enhed i skyen meddelelser til en Azure [Service Bus kø] til øjeblikkelig behandling.

Service Bus sikrer, at pålidelige behandling af interaktive meddelelser, som indeholder det hver meddelelse kontrolpunkterne og tid vinduet-baserede ophævelse kopiering.

> [AZURE.NOTE] En **EventProcessorHost** forekomst er kun en metode til at behandle interaktive meddelelser. Andre indstillinger omfatter [Azure Service strukturen] [ lnk-service-fabric] og [Azure Stream Analytics][lnk-stream-analytics].

I slutningen af dette selvstudium skal køre du tre Java console apps:

* **simuleret enhed**, en ændret version af appen oprettede i selvstudium [komme i gang med IoT Hub] sender datapunkt enhed til skyen meddelelser hver anden og interaktive enhed i skyen meddelelser hvert 10. Denne app bruger AMQP-protokollen til at kommunikere med IoT Hub.
* **proces-d2c-meddelelser, der** bruger klassen [EventProcessorHost] til at hente meddelelser fra begivenhed Hub-kompatible slutpunkt. Det og derefter pålideligt gemmer datapunkt meddelelser i Azure blob-lager og videresender interaktive meddelelser til en tjeneste Bus kø.
* **proces-interaktive-meddelelser** i køer deaktivere de interaktive meddelelser fra Service Bus køen.

> [AZURE.NOTE] IoT Hub har SDK understøttelse af mange Enhedsplatforme og sprog, herunder C, Java og JavaScript. Du kan finde oplysninger om, hvordan du erstatte simuleret enheden i dette selvstudium med en fysisk enhed, og hvordan du knytter enheder til en IoT-Hub, [Azure IoT Developer Center].

Dette selvstudium bruges direkte i andre måder at bruge begivenhed Hub-kompatible meddelelser, som [HDInsight (Hadoop)] projekter. Yderligere oplysninger finder du [Azure IoT Hub developer guide - enhed til skyen].

For at fuldføre dette selvstudium skal bruge du følgende:

+ En komplet arbejdsversion af selvstudiet [komme i gang med IoT Hub] .

+ Java SE 8. <br/> [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Java til dette selvstudium på Windows eller Linux.

+ Maven 3.  <br/> [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Maven til dette selvstudium på Windows eller Linux.

+ En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) på blot et par minutter.

Du skal have et grundlæggende kendskab til [Azure-lager] og [Azure Service Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Sende interaktive meddelelser fra en simuleret enhed

I dette afsnit, skal redigere du programmet simuleret enhed, du oprettede i [komme i gang med IoT Hub] selvstudiet for at sende interaktive enhed i skyen meddelelser til IoT hubben.

1. Brug en teksteditor til at åbne filen simulated-device\src\main\java\com\mycompany\app\App.java. Denne fil indeholder kode for appen **simuleret enhed** , du oprettede i selvstudium i [komme i gang med IoT Hub] .

2. Føje klassen følgende indlejrede til klassen **App** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Denne klasse ligner klassen **MessageSender** i projektet **simuleret enhed** . Den eneste forskel er, at du nu angive egenskaben **MessageId** system, og en brugerdefineret egenskab kaldet **messageType**.
    Koden, tildeler egenskaben **MessageId** et globalt entydigt id (UUID). Service Bus kan bruge dette id til at deaktivere duplikere de meddelelser, den modtager. Eksemplet bruges egenskaben **messageType** til at skelne interaktive fra datapunkt meddelelser. Programmet overfører disse oplysninger i meddelelsesegenskaber for i stedet for i meddelelsesteksten, så begivenhed processoren ikke behøver at deserialisere meddelelsen til at udføre routing af.

    > [AZURE.NOTE] Det er vigtigt at oprette **MessageId** bruges til at deaktivere duplikere interaktive meddelelser i koden enhed. Netværkskommunikation med forbigående eller andre fejl, kan medføre flere retransmission af den samme meddelelse fra enheden. Du kan også bruge en semantisk meddelelse som en hash af datafelter relevante meddelelse i stedet for et UUID-ID.

3. Ændre den **primære** metode til at sende begge interaktive meddelelser og datapunkt meddelelser som vist i følgende kodestykke:

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Gem og Luk filen simulated-device\src\main\java\com\mycompany\app\App.java.

    > [AZURE.NOTE] Dette selvstudium implementerer for at sikre en enkel, ikke en politik for prøv igen. I fremstilling kode, skal du implementerer en politik om forsøg igen som eksponentiel backoff, som foreslået i MSDN-artikel [Midlertidige håndtering af fejl].

5. For at opbygge programmet **simuleret enhed** ved hjælp af Maven skal du udføre følgende kommando kommandoprompten i mappen simuleret enhed:

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>Procesfarver enhed i skyen-meddelelser

I dette afsnit, skal oprette du en Java console-app, der behandler enhed i skyen meddelelser fra IoT Hub. Iot Hub Fremviser en [begivenhed Hub]-kompatible slutpunkt til at aktivere et program tilladelse til at læse enhed i skyen meddelelser. Dette selvstudium bruger klassen [EventProcessorHost] til at behandle disse meddelelser i en console-app. Se [Introduktion til begivenhed Hubs] selvstudiet kan finde flere oplysninger om, hvordan du behandle meddelelser fra begivenhed Hubs.

Den største udfordring, når du implementere pålidelig lagringen af datapunkt meddelelser eller videresendelse af interaktive meddelelser er, at behandling af hændelse er afhængig af den meddelelse forbruger til at levere kontrolpunkter til dens status. Desuden at opnå en høj overførselshastighed, når du læser fra begivenhed Hubs du kan give kontrolpunkter i store batches. Denne metode opretter muligheden for dublerede behandling for et stort antal meddelelser, hvis der er en fejl, og du er kommet til det forrige kontrolpunkt. I dette selvstudium skal du se Sådan synkroniseres Azure-lager skriver og Service Bus ophævelse kopiering windows med **EventProcessorHost** kontrolpunkter.

Hvis du vil skrive pålideligt meddelelser til Azure-lager, stikprøvernes bruger funktionen enkelt blok bekræftelse af [Bloker BLOB][Azure Block Blobs]. Begivenhed processor indsamles meddelelser i hukommelsen, indtil det på tide at give et kontrolpunkt. For eksempel går efter akkumulerede bufferen af meddelelser, når den maksimale blokken størrelse af 4 MB, eller når Service Bus ophævelse kopiering tidsramme. Før kontrolpunktet bekræfter koden derefter en ny blok til blob.

Begivenhed processor bruger begivenhed Hubs meddelelse forskyder som blok id'er. Denne funktion gør det muligt for begivenhed processor skal udføre en kontrol af ophævelse kopiering, før forpligter det nye blokering til lagring, tager sig af nedbrud muligt mellem gemmer en blok og kontrolpunktet.

> [AZURE.NOTE] Dette selvstudium bruger en enkelt Azure-lager-konto til at skrive alle de meddelelser, der er hentet fra IoT Hub. Se [Azure-lager skalerbarhed retningslinjer]for at afgøre, hvis du vil bruge flere Azure-lager-konti i din løsning.

Programmet bruger funktionen Service Bus ophævelse kopiering for at undgå dubletter, når det behandler interaktive meddelelser. Simuleret enheden stempler hver interaktive meddelelse med et entydigt **MessageId**. Dette id Service Bus til at sikre, at i tidsramme angivne ophævelse kopiering ingen to meddelelser med det samme **MessageId** leveres til modtagerne. Denne ophævelse kopiering, sammen med hver meddelelse fuldførelse semantik leveres af Service Bus køer gør det nemt at implementere pålidelig behandling af interaktive meddelelser.

Hvis du vil sikre dig, at ingen meddelelse igen uden for vinduet ophævelse kopiering, synkroniserer koden **EventProcessorHost** kontrolpunkt ordning med vinduet Service Bus kø ophævelse kopiering. Denne synkronisering er udført af at tvinge en kontrolpunkt mindst én gang, hver gang vinduet ophævelse kopiering går (i dette selvstudium vinduet er en time).

> [AZURE.NOTE] Dette selvstudium bruger en enkelt partitioneret Service Bus kø for alle de interaktive meddelelser hentes fra IoT Hub processen. Finde flere oplysninger om, hvordan du bruger Service Bus køer til at opfylde skalerbarhed kravene i din løsning, i dokumentationen til [Azure Service Bus] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Klargøre en Azure-lager-konto og en Service Bus kø

Hvis du vil bruge klassen [EventProcessorHost] , skal du have en Azure-lager-konto for at aktivere **EventProcessorHost** til at registrere kontrolpunkt oplysninger. Du kan bruge en eksisterende konto Azure-lager, eller Følg vejledningen i [Om Azure-lager] for at oprette en ny. Skal du notere Azure-lager konto forbindelsesstrengen.

> [AZURE.NOTE] Når du kopierer og indsætter forbindelsesstrengen Azure-lager-konto, skal du kontrollere ikke indeholder mellemrum inkluderet.

Du skal også en Service Bus kø til at aktivere pålidelig behandling af interaktive meddelelser. Du kan oprette en kø fra et program, med et vindue til en time ophævelse kopiering, som beskrevet i [Sådan bruges Bus servicekøer][Service Bus kø]. Du kan også bruge [Azure klassisk portal][lnk-classic-portal], ved at følge disse trin:

1. Klik på **Ny** i nederste venstre hjørne. Klik derefter på **App tjenester** > **Service Bus** > **kø** > **Brugerdefineret oprette**. Angiv navnet **d2ctutorial**, vælge et område, og brug en eksisterende navneområde eller oprette en ny. Skal du notere navneområdenavnet, du vil have den senere i dette selvstudium. Vælg **aktivere registrering af dubletter**på den næste side, og Angiv den **duplikere registrering historik tidsramme** til en time. Derefter skal du markere afkrydsningsfeltet i nederste højre hjørne for at gemme din kø konfiguration.

    ![Oprette en kø Azure-portalen][30]

2. Klik på **d2ctutorial**på listen over Service Bus køer, og klik derefter på **Konfigurer**. Opret to delt adgang politikker, én kaldet **sende** med **sende** tilladelser og én kaldet **lytte** med **lytte** tilladelser. Bemærk de **primære nøgle** -værdier for begge politikker, du skal bruge dem senere i dette selvstudium. Når du er færdig, skal du klikke på **Gem** nederst.

    ![Konfigurere en kø Azure-portalen][31]

### <a name="create-the-event-processor"></a>Oprette en begivenhed processor

I dette afsnit, skal oprette du en Java program tilladelse til at behandle meddelelser fra begivenhed Hub-kompatible slutpunkt.

Den første opgave er at tilføje en Maven projekt, der kaldes **proces-d2c-meddelelser** , der modtager enhed i skyen meddelelser fra IoT Hub begivenhed Hub-kompatible slutpunkt og dirigerer disse meddelelser til andre back end-tjenester.

1. Opret en Maven projekt, der kaldes **proces-d2c-meddelelser** ved hjælp af følgende kommando i din kommandoprompt i den iot-java-get-gang mappe, du oprettede i [komme i gang med IoT Hub] selvstudiet. Bemærk, at dette er en enkelt, langt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå til den nye mappe i processen-d2c-meddelelser din kommandoprompt.

3. Ved hjælp af en teksteditor, Åbn filen pom.xml i mappen proces-d2c-meddelelser og tilføje følgende afhængigheder til noden **afhængigheder** . Disse afhængigheder gør det muligt at bruge azure-eventhubs, azure-eventhubs-eph og azure-servicebus pakker i programmet til at interagere med dine IoT hub og Service Bus kø:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Gem og Luk filen pom.xml.

Den næste opgave er at tilføje en **ErrorNotificationHandler** klasse til projektet.

1. Brug en teksteditor til at oprette en process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java fil. Tilføje følgende kode i filen for at få vist fejlmeddelelser fra **EventProcesssorHost** forekomsten:

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Gem og Luk filen ErrorNotificationHandler.java.

Nu kan du tilføje en klasse, der implementerer grænsefladen **IEventProcessor** . Klassen **EventProcessorHost** kalder klassen for at behandle enhed i skyen meddelelser, der er modtaget fra IoT Hub. Koden i klassen implementerer logik for at gemme meddelelser sikkert i en blob beholder og videresende interaktive meddelelser til Service Bus køen.

Metoden **onEvents** indstiller variablen **latestEventData** , der registrerer forskydning og sekvens antallet af den seneste meddelelse, der læses af denne begivenhed processor. Husk, at hver processor er ansvarlig for en enkelt partition. Metoden **onEvents** derefter modtager en mængde meddelelser fra IoT-Hub, og behandler dem på følgende måde: den sender interaktive meddelelser til Service Bus køen og føjer data punkt meddelelser til **toAppend** hukommelsesbufferen. Hvis hukommelsesbufferen når 4 MB Bloker grænse, eller ophævelse kopiering tid windows går (en time efter det sidste kontrolpunkt i dette selvstudium), udløser metoden et kontrolpunkt.

Metoden **AppendAndCheckPoint** først genererer en **blockId** for blokering skal føjes til blob. Azure-lager kræver, at alle blokere-id'er for at have samme længde, så metoden pads med foranstillede nuller forskydning. Derefter, hvis en blok med dette ID er allerede blob, metoden overskriver det med det aktuelle buffer indhold.

> [AZURE.NOTE] Dette selvstudium bruger for at forenkle koden, en enkelt blob per partition til hvor meddelelserne skal gemmes. En rigtig løsning vil implementere fil enten ved at oprette flere filer efter et stykke tid, eller når de når en bestemt størrelse. Husk, at en blok Azure blob kan indeholde højst 195 GB data.

Den næste opgave er at implementere grænsefladen **IEventProcessor** :

1. Brug en teksteditor til at oprette en process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java fil.

2. Tilføj følgende importerer og klassedefinition til filen EventProcessor.java. Klassen **EventProcessor** implementerer grænsefladen **IEventProcessor** , der definerer adfærden for begivenhed Hubs klienten:

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Føje følgende metoder til klassen **EventProcessor** at implementere grænsefladen **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Føje følgende klasse niveau variabler til klassen **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Føje en **AppendAndCheckPoint** metode til den følgende signatur til klassen **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Føj følgende kode til **AppendAndCheckPoint** metoden til at hente den aktuelle meddelelse forskydning og sekvens nummer i partitionen:

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Bruge den aktuelle forskydning værdi til at oprette en forekomst af **BlockEntry** til den næste blok gemme blob i metoden **AppendAndCheckPoint** :

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Overfør det seneste sæt af meddelelser til Bloker blob i metoden **AppendAndCheckPoint** og hente den aktuelle liste over dokumentkomponenter:

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Oprette indledende blokering i en ny blob eller føje blokering til den eksisterende blob i metoden **AppendAndCheckPoint** :

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Opret et kontrolpunkt på partition til sidst i metoden **AppendAndCheckPoint** og forberede dig på at gemme den næste blok af meddelelser:

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Tilføj følgende kode for at modtage meddelelser fra IoT Hub slutpunkt og videresende interaktive meddelelser til Service Bus køen i metoden **onEvents** . Derefter kalde metoden **AppendAndCheckPoint** , når blokering er fuld, eller timeouten udløber:

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Til sidst i metoden **onEvents** Tilføj en 'Ellers hvis'-delsætning for at ringe **AppendAndCheckPoint** , hvis timeouten udløber, mens der er ingen meddelelser, der kommer fra IoT Hub:

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Gemme ændringerne i filen EventProcessor.java.

Den sidste opgave i projektet **proces-d2c-meddelelser, der** er at tilføje kode til den **primære** metode, der starter en **EventProcessorHost** forekomst.

1. Brug en teksteditor til at åbne filen process-d2c-messages\src\main\java\com\mycompany\app\App.java.

2. Tilføj følgende **importere** sætninger i filen:

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Tilføj følgende klasse niveau variabel til **App** -klasse. Erstat **{yourstorageaccountconnectionstring}** med forbindelsesstrengen Azure-lager-konto, du har oprettet en note af tidligere i afsnittet [klargøre en Azure-lager-konto og en Service Bus kø](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Tilføj følgende klasse niveau variabler til **App** -klasse og erstatte **{yourservicebusnamespace}** med din tjeneste Bus navneområde og **{yourservicebussendkey}** med din kø **sende** nøgle. Du tidligere har foretaget en note af navneområde og **lytte** nøgleværdier i afsnittet [klargøre en Azure-lager-konto og en Service Bus kø](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Tilføj følgende klasse niveau variabler til **App** -klasse. Erstat **{youreventhubcompatibleendpoint}** med værdien begivenhed Hub-kompatible slutpunkt. Slutpunktsværdien ligner **hans... navneområde** så skal du fjerne den **sb: / /** præfiks og **.servicebus.windows.net/** suffiks. Erstat **{youreventhubcompatiblename}** med navnet på begivenheden Hub-kompatible. Erstat **{youriothubkey}** med tasten **iothubowner** . Du har oprettet en note af disse værdier i [oprette en IoT Hub] [ lnk-create-an-iot-hub] afsnit i *Introduktion til Azure IoT Hub til Java* selvstudiet:

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Ændre signaturen for den **primære** metode på følgende måde:

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Føj følgende kode til den **primære** metode til at få en reference til objektbeholderen blob, der gemmer meddelelser:

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Føj følgende kode til den **primære** metode til at få en reference til tjenesten Service Bus:

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. I den **primære** metode, konfigurere og oprette en **EventProcessorHost** forekomst. Indstillingen **setInvokeProcessorAfterReceiveTimeout** sikrer, at **EventProcessorHost** forekomsten aktiverer metoden **onEvents** i grænsefladen **IEventProcessor** , selvom der er ingen meddelelser til at behandle. Metoden **onEvents** starter altid derefter metoden **AppendAndCheckPoint** når timeout udløber.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Registrere **IEventProcessor** implementeringen med **EventProcessorHost** forekomsten i den **primære** metode:

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Til sidst skal du føje logik til den **primære** metode til at lukke **EventProcessorHost** forekomsten:

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Gem og Luk filen process-d2c-messages\src\main\java\com\mycompany\app\App.java.

13. For at opbygge programmet **proces-d2c-meddelelser** ved hjælp af Maven skal du udføre følgende kommando kommandoprompten i mappen proces-d2c-meddelelser:

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Modtage interaktive meddelelser

I dette afsnit, skal skrive du en Java console-app, modtager de interaktive meddelelser fra Service Bus køen.

Den første opgave er at tilføje en Maven projekt, der kaldes **proces-interaktive-meddelelser** , der modtager meddelelser, der sendes på Service Bus køen **EventProcessor** forekomster.

1. Opret en Maven projekt, der kaldes **proces-interaktive-meddelelser** ved hjælp af følgende kommando i din kommandoprompt i den iot-java-get-gang mappe, du oprettede i [komme i gang med IoT Hub] selvstudiet. Bemærk, at dette er en enkelt, langt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå til den nye mappe i processen-interaktive-meddelelser din kommandoprompt.

3. Ved hjælp af en teksteditor, Åbn filen pom.xml i mappen proces-interaktive-meddelelser og tilføje følgende afhængigheden til noden **afhængigheder** . Denne afhængighed kan du bruge pakken azure-servicebus i dit program til at interagere med din tjeneste Bus kø:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Gem og Luk filen pom.xml.

Den næste opgave er at tilføje kode for at hente meddelelser fra Service Bus køen.

1. Brug en teksteditor til at åbne filen process-interactive-messages\src\main\java\com\mycompany\app\App.java.

2. Tilføj følgende `import` sætninger til filen:

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Tilføj følgende klasse niveau variabler til **App** -klasse og erstatte **{yourservicebusnamespace}** med din tjeneste Bus navneområde og **{yourservicebuslistenkey}** med din kø **lytte** nøgle. Du tidligere har foretaget en note af navneområde og **lytte** nøgleværdier i afsnittet [klargøre en Azure-lager-konto og en Service Bus kø](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Føje klassen følgende indlejrede til klassen **App** til at modtage meddelelser fra køen:

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Ændre signaturen for den **primære** metode på følgende måde:

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Tilføj følgende kode for at starte lytter efter nye meddelelser i den **primære** metode:

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Gem og Luk filen process-interactive-messages\src\main\java\com\mycompany\app\App.java.

8. For at opbygge programmet **proces-interaktive-meddelelser** ved hjælp af Maven skal du udføre følgende kommando kommandoprompten i mappen proces-interaktive-meddelelser:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre de tre programmer.

1.  Hvis du vil køre programmet **proces-interaktive-meddelelser** , i en kommandoprompt eller shell gå til mappen proces-interaktive-meddelelser og udføre følgende kommando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kør processen-interaktive-meddelelser][processinteractive]

2.  Hvis du vil køre programmet **proces-d2c-meddelelser** , i en kommandoprompt eller shell gå til mappen proces-d2c-meddelelser og udføre følgende kommando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kør processen-d2c-meddelelser][processd2c]

3.  Hvis du vil køre programmet **simuleret enhed** , i en kommandoprompt eller shell gå til mappen simuleret enhed og udføre følgende kommando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Køre simuleret enhed][simulateddevice]

> [AZURE.NOTE] For at se opdateringer i din blob, skal du reducere konstanten **MAX_BLOCK_SIZE** i klassen **StoreEventProcessor** til en mindre værdi, som **1024**. Denne ændring er nyttigt, fordi det tager lang tid at Tag størrelsesgrænsen blok med de data, der er sendt af simuleret enheden. Med mindre Bloker behøver du ikke at vente så lang tid for at se blob blive oprettet og opdateret. Ved hjælp af en større Bloker gør imidlertid programmet mere SVG.

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært Sådan pålideligt behandle datapunkt og interaktive enhed i skyen meddelelser ved hjælp af klassen [EventProcessorHost] .

[Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub] [ lnk-c2d] viser, hvordan du sender meddelelser til dine enheder fra din back-end.

For at se eksempler på komplette til slut-løsninger, der bruger IoT-Hub skal du se [Azure IoT pakke][lnk-suite].

Hvis du vil vide mere om udvikling af løsninger med IoT Hub skal du se [IoT Hub Developer Guide].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Azure blob-lager]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus kø]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT Hub udvikler vejledning - enhed til skyen]: iot-hub-devguide-messaging.md

[Azure-lager]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub Developer Guide]: iot-hub-devguide.md
[Komme i gang med IoT-Hub]: iot-hub-java-java-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Om Azure-lager]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introduktion til begivenhed Hubs]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure lagerplads skalerbarhed retningslinjer]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub