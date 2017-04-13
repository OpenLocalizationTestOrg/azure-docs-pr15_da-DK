## <a name="receive-messages-with-eventprocessorhost"></a>Modtage meddelelser med EventProcessorHost

[EventProcessorHost][] er en .NET-klasse, der forenkler modtagelse begivenheder fra begivenhed Hubs ved administration af fast kontrolpunkter, og parallelt modtager fra disse begivenhed Hubs. Brug af [EventProcessorHost][], kan du opdele begivenheder på tværs af flere modtagere, selvom forskellige noder som vært. I dette eksempel vises, hvordan du bruger [EventProcessorHost][] til en enkelt modtager. [Behandling af skaleret ud begivenhed][] stikprøvernes viser, hvordan du bruger [EventProcessorHost][] med flere modtagere.

Hvis du vil bruge [EventProcessorHost][], skal du have en [Azure-lager konto][]:

1. Logge på [Azure-portalen][], og klik på **Ny** øverst til venstre på skærmen.

2. Klik på **Data + lagerplads**, og klik derefter på **lagerplads konto**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)

3. Skriv et navn til kontoen lagerplads i bladet **Opret lagerplads konto** . Vælg en Azure-abonnement, ressourcegruppe og placering, hvor oprette ressourcen. Klik derefter på **Opret**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)

4. Klik på kontoen nyoprettet lagerplads på listen over lagerplads konti.

5. Klik på **Access-taster**i bladet lagerplads konto. Kopiér værdien af **nøgle1** bruge senere i dette selvstudium.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)

4. Oprette et projekt med visuelle C# skrivebordet App ved hjælp af skabelonen **Console programmet** projekt i Visual Studio. Navngive projektet **modtager**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)

5. I Solution Explorer, højreklik på løsningen, og klik derefter på **Administrer NuGet pakker til løsning**.

6. Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Sørg for, at navnet på projektet (**modtager**) er angivet i feltet **versioner** . Klik på **Installer**, og Accepter vilkår for anvendelse.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)

    Visual Studio henter, installerer og tilføjer en reference til [Azure Service Bus begivenhed Hub - EventProcessorHost NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alle dens afhængigheder.

7. Højreklik på **modtageren** projektet, skal du klikke på **Tilføj**, og klik derefter på **klasse**. Navngiv den nye klasse **SimpleEventProcessor**, og klik derefter på **Tilføj** for at oprette klassen.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)

8. Tilføj følgende udtalelser øverst i filen SimpleEventProcessor.cs:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    Erstatte derefter følgende kode for brødteksten i klassen:

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ```

    Denne klasse kaldes ved **EventProcessorHost** til proceshændelser, der modtages fra begivenhed-hubben. Bemærk, at den `SimpleEventProcessor` klasse bruger en stopuret til at ringe med jævne mellemrum metoden kontrolpunkt på **EventProcessorHost** konteksten. Dette sikrer, at hvis modtageren er genstartet, mistes ikke mere end fem minutter for at behandle arbejde.

9. Tilføj følgende i klassen **Program** `using` sætningen øverst i filen:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    Erstat derefter den `Main` metode i den `Program` klasse med følgende kode, erstatter begivenhed Hub navnet og navneområde niveau forbindelsesstrengen, du gemte tidligere, og lagerplads konto og nøgle, du kopierede i ovenstående afsnit. 

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

> [AZURE.NOTE] Dette selvstudium bruger en enkelt forekomst af [EventProcessorHost][]. Hvis du vil øge kapaciteten, anbefales det, at du kører flere forekomster af [EventProcessorHost][], som vist i eksemplet [skaleret ud behandling af hændelse][] . I disse tilfælde forskellige forekomster automatisk være koordineret med hinanden til justering af belastning de hændelser, der er modtaget. Hvis du vil flere modtagere til hver proces *alle* hændelserne, skal du bruge **ConsumerGroup** konceptet. Når du modtager begivenheder fra forskellige computere, kan det være praktisk at angive navne til [EventProcessorHost][] forekomster baseret på computere (eller roller) i som de er installeret. Du kan finde flere oplysninger om disse emner, [Begivenhed Hubs oversigt][] og [Begivenhed Hubs Programming Guide][] emner.

<!-- Links -->
[Oversigt over Hubs begivenhed]: ../articles/event-hubs/event-hubs-overview.md
[Begivenhed Hubs Programming vejledning]: ../articles/event-hubs/event-hubs-programming-guide.md
[Skaleret begivenhed behandling]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Azure lagerplads-konto]: ../articles/storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Azure-portalen]: https://portal.azure.com