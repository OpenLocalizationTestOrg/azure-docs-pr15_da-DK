<properties
    pageTitle="Behandle IoT Hub enhed i skyen meddelelser (.Net) | Microsoft Azure"
    description="Følg dette selvstudium for at få mere at vide nyttige mønstre for at behandle IoT Hub enhed i skyen meddelelser."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Selvstudium: Sådan behandles IoT Hub enhed i skyen meddelelser ved hjælp af .net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der gør det muligt for pålidelig og sikker tovejs-kommunikation mellem millioner af IoT enheder og et program igen slutningen. Andre selvstudier ([komme i gang med IoT Hub] og [sende cloud-til-enhed meddelelser med IoT Hub][lnk-c2d]) viser, hvordan du den grundlæggende enhed i skyen og skyen-til-enhed SMS bruges af IoT Hub.

Dette selvstudium bygger på den kode, der vises i [Introduktion til IoT Hub] selvstudiet, og der vises to SVG mønstre, som du kan bruge til at behandle enhed i skyen meddelelser:

- Pålidelige opbevaring af enhed i skyen meddelelser i [Azure blob-lager]. Et almindelige scenarie er *kolde sti* analytics, hvor du gemmer telemetridata i BLOB skal bruges som input til analytics processer. Disse processer kan styres af funktioner som [Azure Data Factory] eller [HDInsight (Hadoop)] stablen.

- Pålidelige behandling af *interaktive* enhed i skyen meddelelser. Enhed i skyen meddelelser er interaktive, når de er øjeblikkelig udløsere til et sæt handlinger i programmet back-end. En enhed kan for eksempel sender en alarm, der udløser indsætning af en brugertilladelse i et CRM-system. Derimod feed *datapunkt* meddelelser blot i en analytics-program. For eksempel er temperaturen telemetri fra en enhed, der skal gemmes til senere analyse meddelelsen data punkt.

Da IoT Hub Fremviser en [Begivenhed Hub][lnk-event-hubs]-kompatible slutpunkt, der skal modtage meddelelser enhed i skyen, dette selvstudium bruger en [EventProcessorHost] forekomst. Denne forekomst:

* Pålideligt indeholder *datapunkt* meddelelser i Azure blob-lager.
* Videresender *interaktive* enhed i skyen meddelelser til en Azure [Service Bus kø] til øjeblikkelig behandling.

Service Bus sikrer, at pålidelige behandling af interaktive meddelelser, som indeholder det hver meddelelse kontrolpunkterne og tid vinduet-baserede ophævelse kopiering.

> [AZURE.NOTE] En **EventProcessorHost** forekomst er kun en metode til at behandle interaktive meddelelser. Andre indstillinger omfatter [Azure Service strukturen] [ lnk-service-fabric] og [Azure Stream Analytics][lnk-stream-analytics].

I slutningen af dette selvstudium skal køre du tre Windows console apps:

* **SimulatedDevice**, en ændret version af appen oprettet i [komme i gang med IoT Hub] selvstudiet, sender data punkt enhed i skyen meddelelser hver anden og interaktive enhed i skyen meddelelser hvert 10. Denne app bruger AMQP-protokollen til at kommunikere med IoT Hub.
* **ProcessDeviceToCloudMessages** bruger klassen [EventProcessorHost] til at hente meddelelser fra begivenhed Hub-kompatible slutpunkt. Det og derefter pålideligt lagrer data punkt meddelelser i Azure blob-lager og videresender interaktive meddelelser til en tjeneste Bus kø.
* **ProcessD2CInteractiveMessages** køer deaktivere de interaktive meddelelser fra Service Bus køen.

> [AZURE.NOTE] IoT Hub har SDK understøttelse af mange Enhedsplatforme og sprog, herunder C, Java og JavaScript. Hvis du vil lære at erstatte den simulerede enhed i dette selvstudium med en fysisk enhed, og hvordan du knytter enheder til en IoT-Hub, se [Azure IoT Developer Center].

Dette selvstudium bruges direkte i andre måder at bruge begivenhed Hub-kompatible meddelelser, som [HDInsight (Hadoop)] projekter. Yderligere oplysninger finder du [Azure IoT Hub developer guide - enhed til skyen].

For at fuldføre dette selvstudium skal bruge du følgende:

+ Microsoft Visual Studio 2015.

+ En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) på blot et par minutter.

Du skal have et grundlæggende kendskab til [Azure-lager] og [Azure Service Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Sende interaktive meddelelser fra en simuleret enhed

I dette afsnit, skal redigere du programmet simuleret enhed, du oprettede i [komme i gang med IoT Hub] selvstudiet for at sende interaktive enhed i skyen meddelelser til IoT hubben.

1. Tilføj følgende metode til klassen **Program** i project **SimulatedDevice** i Visual Studio.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Denne metode er lig med metoden **SendDeviceToCloudMessagesAsync** i **SimulatedDevice** projektet. Den eneste forskel er, at du nu angive egenskaben **MessageId** system, og en egenskab kaldet **messageType**.
    Koden, tildeler egenskaben **MessageId** et globalt entydigt id (GUID). Service Bus kan bruge dette id til at deaktivere duplikere de meddelelser, den modtager. Eksemplet bruges egenskaben **messageType** til at skelne interaktive fra data punkt meddelelser. Programmet overfører disse oplysninger i meddelelsesegenskaber for i stedet for i meddelelsesteksten, så begivenhed processoren ikke behøver at deserialisere meddelelsen til at udføre routing af.

    > [AZURE.NOTE] Det er vigtigt at oprette **MessageId** bruges til at deaktivere duplikere interaktive meddelelser i koden enhed. Netværkskommunikation med forbigående eller andre fejl, kan medføre flere retransmission af den samme meddelelse fra enheden. Du kan også bruge en semantisk meddelelse som en hash af datafelter relevante meddelelse i stedet for en GUID-ID.

2. Tilføj følgende metode i metoden **Main** lige før den `Console.ReadLine()` linje:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Dette selvstudium implementerer for at sikre en enkel, ikke en politik for prøv igen. I fremstilling kode, skal du implementerer en politik om forsøg igen som eksponentiel backoff, som foreslået i MSDN-artikel [Midlertidige håndtering af fejl].

## <a name="process-device-to-cloud-messages"></a>Procesfarver enhed i skyen-meddelelser

I dette afsnit, skal oprette du en Windows-konsollen app, der behandler enhed i skyen meddelelser fra IoT Hub. Iot Hub Fremviser en [begivenhed Hub]-kompatible slutpunkt til at aktivere et program tilladelse til at læse enhed i skyen meddelelser. Dette selvstudium bruger klassen [EventProcessorHost] til at behandle disse meddelelser i en console-app. Se [Introduktion til begivenhed Hubs] selvstudiet kan finde flere oplysninger om, hvordan du behandle meddelelser fra begivenhed Hubs.

Udfordringen, når du implementerer pålidelig lagringen af datapunkt meddelelser eller videresendelse af interaktive meddelelser er behandling af denne hændelse er afhængig af den meddelelse forbruger til at levere kontrolpunkter til dens status. Desuden at opnå en høj overførselshastighed, når du læser fra begivenhed Hubs du kan give kontrolpunkter i store batches. Denne metode opretter muligheden for dublerede behandling for et stort antal meddelelser, hvis der er en fejl, og du er kommet til det forrige kontrolpunkt. I dette selvstudium skal du se Sådan synkroniseres Azure-lager skriver og Service Bus ophævelse kopiering windows med **EventProcessorHost** kontrolpunkter.

Hvis du vil skrive pålideligt meddelelser til Azure-lager, stikprøvernes bruger funktionen enkelt blok bekræftelse af [Bloker BLOB][Azure Block Blobs]. Begivenhed processor indsamles meddelelser i hukommelsen, indtil det på tide at give et kontrolpunkt. For eksempel går efter akkumulerede bufferen af meddelelser, når den maksimale blokken størrelse af 4 MB, eller når Service Bus ophævelse kopiering tidsramme. Før kontrolpunktet bekræfter koden derefter en ny blok til blob.

Begivenhed processor bruger begivenhed Hubs meddelelse forskyder som blok id'er. Denne funktion gør det muligt for begivenhed processor skal udføre en kontrol af ophævelse kopiering, før forpligter det nye blokering til lagring, tager sig af nedbrud muligt mellem gemmer en blok og kontrolpunktet.

> [AZURE.NOTE] Dette selvstudium bruger en enkelt Azure-lager-konto til at skrive alle de meddelelser, der er hentet fra IoT Hub. Se [Azure-lager skalerbarhed retningslinjer]for at afgøre, hvis du vil bruge flere Azure-lager-konti i din løsning.

Programmet bruger funktionen Service Bus ophævelse kopiering for at undgå dubletter, når det behandler interaktive meddelelser. Simuleret enheden stempler hver interaktive meddelelse med et entydigt **MessageId**. Disse id'er aktivere Service Bus til at sikre, at i tidsramme angivne ophævelse kopiering ingen to meddelelser med det samme **MessageId** leveres til modtagerne. Denne ophævelse kopiering, sammen med hver meddelelse fuldførelse semantik leveres af Service Bus køer gør det nemt at implementere pålidelig behandling af interaktive meddelelser.

Hvis du vil sikre dig, at ingen meddelelse igen uden for vinduet ophævelse kopiering, synkroniserer koden **EventProcessorHost** kontrolpunkt ordning med vinduet Service Bus kø ophævelse kopiering. Denne synkronisering er udført af at tvinge en kontrolpunkt mindst én gang, hver gang vinduet ophævelse kopiering går (i dette selvstudium vinduet er en time).

> [AZURE.NOTE] Dette selvstudium bruger en enkelt partitioneret Service Bus kø for alle de interaktive meddelelser hentes fra IoT Hub processen. Finde flere oplysninger om, hvordan du bruger Service Bus køer til at opfylde skalerbarhed kravene i din løsning, i dokumentationen til [Azure Service Bus] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Klargøre en Azure-lager-konto og en Service Bus kø
Hvis du vil bruge klassen [EventProcessorHost] , skal du have en Azure-lager-konto for at aktivere **EventProcessorHost** til at registrere kontrolpunkt oplysninger. Du kan bruge en eksisterende konto Azure-lager, eller Følg vejledningen i [Om Azure-lager] for at oprette en ny. Skal du notere Azure-lager konto forbindelsesstrengen.

> [AZURE.NOTE] Når du kopierer og indsætter forbindelsesstrengen Azure-lager-konto, skal du kontrollere ikke indeholder mellemrum inkluderet.

Du skal også en Service Bus kø til at aktivere pålidelig behandling af interaktive meddelelser. Du kan oprette en kø fra et program, med et vindue til en time ophævelse kopiering, som beskrevet i [Sådan bruges Bus servicekøer][Service Bus kø]. Du kan også bruge [Azure klassisk portal][lnk-classic-portal], ved at følge disse trin:

1. Klik på **Ny** i nederste venstre hjørne. Klik derefter på **App tjenester** > **Service Bus** > **kø** > **Brugerdefineret oprette**. Angiv navnet **d2ctutorial**, vælge et område, og brug en eksisterende navneområde eller oprette en ny. Vælg **aktivere registrering af dubletter**på den næste side, og Angiv den **duplikere registrering historik tidsramme** til en time. Derefter skal du markere afkrydsningsfeltet i nederste højre hjørne for at gemme din kø konfiguration.

    ![Oprette en kø Azure-portalen][30]

2. Klik på **d2ctutorial**på listen over Service Bus køer, og klik derefter på **Konfigurer**. Opret to delt adgang politikker, én kaldet **sende** med **sende** tilladelser og én kaldet **lytte** med **lytte** tilladelser. Når du er færdig, skal du klikke på **Gem** nederst.

    ![Konfigurere en kø Azure-portalen][31]

3. Klik på **Dashboard** øverst, og klik derefter på **forbindelsesoplysningerne** nederst. Skal du notere af de to forbindelsesstrenge.

    ![Kø dashboard Azure-portalen][32]

### <a name="create-the-event-processor"></a>Oprette en begivenhed processor

1. Klik på **filer**i den aktuelle løsning i Visual Studio for at oprette en visuel C# Windows projekt ved hjælp af skabelonen **Console programmet** project, > **Tilføj** > **Nyt projekt**. Sørg for, at versionen af .NET Framework er 4.5.1 eller nyere. Navngiv projektet **ProcessDeviceToCloudMessages**, og klik på **OK**.

    ![Nyt projekt i Visual Studio][10]

2. Højreklik på **ProcessDeviceToCloudMessages** projektet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**. Dialogboksen **NuGet Package Manager** vises.

3. Søge efter **WindowsAzure.ServiceBus**, klik på **Installer**, og Accepter vilkår for anvendelse. Denne handling henter, installerer og tilføjer en reference til [Azure Service Bus NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus), med alle dens afhængigheder.

4. Søge efter **Microsoft.Azure.ServiceBus.EventProcessorHost**, klik på **Installer**, og Accepter vilkår for anvendelse. Denne handling henter, installerer og tilføjer en reference til [Azure Service Bus begivenhed Hub - EventProcessorHost NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alle dens afhængigheder.

5. Højreklik på **ProcessDeviceToCloudMessages** projektet, skal du klikke på **Tilføj**, og klik derefter på **klasse**. Navngiv den nye klasse **StoreEventProcessor**, og klik derefter på **OK** for at oprette klassen.

6. Tilføj følgende udtalelser øverst i filen StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Erstatte følgende kode for brødteksten i klassen:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    Klassen **EventProcessorHost** kalder klassen for at behandle enhed i skyen meddelelser, der er modtaget fra IoT Hub. Koden i klassen implementerer logik for at gemme meddelelser sikkert i en blob beholder og videresende interaktive meddelelser til Service Bus køen.

    Metoden **OpenAsync** starter variablen **currentBlockInitOffset** , som holder styr på forskydning for den aktuelle i den første besked, der læses af denne begivenhed processor. Husk, at hver processor er ansvarlig for en enkelt partition.

    Metoden **ProcessEventsAsync** modtager en mængde meddelelser fra IoT-Hub, og behandler dem på følgende måde: den sender interaktive meddelelser til Service Bus køen og føjer data punkt meddelelser til hukommelsesbufferen kaldet **toAppend**. Hvis hukommelsesbufferen når grænsen på 4 MB eller ophævelse kopiering tid windows går (en time efter et kontrolpunkt i dette selvstudium), udløser programmet et kontrolpunkt.

    Metoden **AppendAndCheckpoint** først genererer en blockId for blokering skal tilføjes. Azure-lager kræver, at alle blokere-id'er for at have samme længde, så metoden pads med foranstillede nuller - forskydning `currentBlockInitOffset.ToString("0000000000000000000000000")`. Derefter, hvis en blok med dette ID er allerede blob, overskriver metoden det med det aktuelle indhold af bufferen.

    > [AZURE.NOTE] Dette selvstudium bruger for at forenkle koden, en enkelt blob per partition til hvor meddelelserne skal gemmes. En rigtig løsning vil implementere fil enten ved at oprette flere filer efter et stykke tid, eller når de når en bestemt størrelse. Husk, at en blok Azure blob kan indeholde højst 195 GB data.

8. Tilføj følgende **ved hjælp af** sætning øverst i klassen **Program** :

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Ændre metoden **Main** i klassen **Program** på følgende måde. Erstat **{iot hub forbindelsesstrengen}** med **iothubowner** forbindelsesstrengen fra selvstudium [komme i gang med IoT Hub] . Erstat forbindelsesstrengen lagerplads med forbindelsesstrengen du noteret i starten af dette afsnit. Erstat forbindelsesstrengen Service Bus med **sende** tilladelser for køen med navnet **d2ctutorial** du noteret i starten af dette afsnit:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Dette selvstudium bruger for at sikre en enkel, en enkelt forekomst af klassen [EventProcessorHost] . Du kan finde yderligere oplysninger finder [Begivenhed Hubs Programming Guide].

## <a name="receive-interactive-messages"></a>Modtage interaktive meddelelser
I dette afsnit, skal skrive du en Windows-konsollen app, modtager de interaktive meddelelser fra Service Bus køen. Du kan finde flere oplysninger om, hvordan du udvikle en løsning med Service Bus se [opbygge med flere lag programmer med Service Bus][].

1. Oprette et Visual C# Windows projekt ved hjælp af skabelonen **Console programmet** project i den aktuelle løsning i Visual Studio. Navngive projektet **ProcessD2CInteractiveMessages**.

2. Højreklik på **ProcessD2CInteractiveMessages** projektet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**. Denne handling viser vinduet **NuGet pakke Manager** .

3. Søge efter **WindowsAzure.ServiceBus**, klik på **Installer**, og Accepter vilkår for anvendelse. Denne handling henter, installerer og tilføjer en reference til [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus), med alle dens afhængigheder.

4. Tilføj følgende **ved hjælp af** udtalelser øverst i filen **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Til sidst skal du føje følgende linjer til metoden **Main** . Erstatte forbindelsesstrengen med **lytte** tilladelser for køen med navnet **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1.  Højreklik på din løsning i Visual Studio i Solution Explorer, og vælg **Angive start-projekter**. Vælg **flere projekter, Start**og derefter vælge **Start** som handlingen for **ProcessDeviceToCloudMessages**, **SimulatedDevice**og **ProcessD2CInteractiveMessages** projekterne.

2.  Tryk på **F5** for at starte tre console-programmer. Programmet **ProcessD2CInteractiveMessages** skal behandle alle interaktive meddelelser, der sendes fra programmet **SimulatedDevice** .

  ![Tre console-programmer][50]

> [AZURE.NOTE] For at se opdateringer i din blob, skal du reducere konstanten **MAX_BLOCK_SIZE** i klassen **StoreEventProcessor** til en mindre værdi, som **1024**. Denne ændring er nyttigt, fordi det tager lang tid at Tag størrelsesgrænsen blok med de data, der er sendt af simuleret enheden. Med mindre Bloker behøver du ikke at vente så lang tid for at se blob blive oprettet og opdateret. Ved hjælp af en større Bloker gør imidlertid programmet mere SVG.

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært Sådan pålideligt behandle datapunkt og interaktive enhed i skyen meddelelser ved hjælp af klassen [EventProcessorHost] .

[Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub] [ lnk-c2d] viser, hvordan du sender meddelelser til dine enheder fra din back-end.

For at se eksempler på komplette til slut-løsninger, der bruger IoT Hub skal du se [Azure IoT pakke][lnk-suite].

Hvis du vil vide mere om udvikling af løsninger med IoT Hub skal du se [IoT Hub Developer Guide].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Azure blob-lager]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus kø]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT Hub udvikler vejledning - enhed til skyen]: iot-hub-devguide-messaging.md

[Azure-lager]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub Developer Guide]: iot-hub-devguide.md
[Komme i gang med IoT-Hub]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Om Azure-lager]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introduktion til begivenhed Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure lagerplads skalerbarhed retningslinjer]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Begivenhed Hubs Programming vejledning]: ../event-hubs/event-hubs-programming-guide.md
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Opbygge med flere lag programmer med Service Bus]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
