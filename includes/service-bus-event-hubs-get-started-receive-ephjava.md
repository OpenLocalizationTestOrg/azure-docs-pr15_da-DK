## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Modtage meddelelser med EventProcessorHost i Java

EventProcessorHost en Java-klasse, der forenkler modtagelse begivenheder fra begivenhed Hubs ved administration af fast kontrolpunkter og modtager parallelt fra disse begivenhed Hubs. Ved hjælp af EventProcessorHost kan du opdele begivenheder på tværs af flere modtagere, selv når forskellige noder som vært. I dette eksempel vises, hvordan du bruger EventProcessorHost til en enkelt modtager.

###<a name="create-a-storage-account"></a>Oprette en lagerplads-konto

For at kunne bruge EventProcessorHost, skal du have en [Azure-lager konto][]:

1. Logge på [Azure klassisk portal][], og klik på **Ny** nederst på skærmen.

2. Klik på **Data Services**, og klik derefter **lager**og derefter **Hurtig oprettelse**, og skriv derefter et navn til kontoen lagerplads. Vælg dit ønskede område, og klik derefter på **Opret lagerplads konto**.

    ![][11]

3. Klik på kontoen nyoprettede lagerplads, og klik derefter på **Administrer Access-taster**:

    ![][12]

    Kopiér den primære hurtigtast bruge senere i dette selvstudium.

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>Oprette et Java projekt ved hjælp af EventProcessor Host

Biblioteket Java klient for begivenhed Hubs er tilgængelig til brug i Maven projekter fra [Maven centrallager][Maven Package], og der kan refereres til ved hjælp af følgende afhængighed erklæring i projektfilen Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
For forskellige typer build-miljøer, kan du udtrykkeligt hente de seneste frigivne glas filer fra [Maven centrallager] [ Maven Package] eller fra [punktet release fordeling på GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Det følgende eksempel først oprette et nyt Maven projekt til et konsol/shell-program i din foretrukne Java udviklingsmiljø. Klassen bliver kaldt ```ErrorNotificationHandler```.     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. Brug følgende kode til at oprette en ny klasse, kaldet ```EventProcessor```.

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. Oprette en endelige klasse kaldet ```EventProcessorSample```, ved hjælp af følgende kode.

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. Erstat følgende felter med værdierne, der bruges, når du har oprettet den begivenhed Hub og lagerplads konto.

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] Dette selvstudium bruger en enkelt forekomst af EventProcessorHost. Det anbefales, at du kører flere forekomster af EventProcessorHost for at øge kapaciteten. I disse tilfælde forskellige forekomster automatisk være koordineret med hinanden for at indlæse balance de hændelser, der er modtaget. Hvis du vil flere modtagere til hver proces *alle* hændelserne, skal du bruge **ConsumerGroup** konceptet. Når du modtager begivenheder fra forskellige computere, kan det være praktisk at angive navne til EventProcessorHost forekomster baseret på computere (eller roller) i som de er installeret.

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Azure lagerplads-konto]: ../articles/storage/storage-create-storage-account.md
[Azure klassisk portal]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

