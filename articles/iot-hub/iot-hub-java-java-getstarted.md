<properties
    pageTitle="Azure IoT Hub til Java Introduktion | Microsoft Azure"
    description="Azure IoT hubben med Java komme i gang selvstudium. Bruge Azure IoT Hub og Java med Microsoft Azure IoT SDK'er for at implementere en Internet ting løsning."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Introduktion til Azure IoT Hub til Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutningen af dette selvstudium har du tre Java console-programmer:

* **oprette-enhed-identitet**, der opretter en enhed identitet og tilknyttede sikkerhed for at oprette forbindelse mellem enheden simuleret.
* **Læs-d2c-meddelelser**, som viser telemetri sendt af enheden simuleret.
* **simuleret enhed**, som opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, og sender en meddelelse til telemetri hver anden ved hjælp af AMQP protokollen.

> [AZURE.NOTE] I artiklen [IoT Hub SDK'er] [ lnk-hub-sdks] indeholder oplysninger om de forskellige SDK'er, som du kan bruge til at opbygge begge programmer til at køre på enheder og din løsning back-end.

For at fuldføre dette selvstudium skal bruge du følgende:

+ Java SE 8. <br/> [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Java til dette selvstudium på Windows eller Linux.

+ Maven 3.  <br/> [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Maven til dette selvstudium på Windows eller Linux.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Som det sidste trin, skal du notere den **primære nøgle** -værdi, og klik derefter på **meddelelser**. På bladet **meddelelser** skal du notere **begivenhed Hub-kompatible navn** og **begivenhed Hub-kompatible slutpunkt**. Du har brug for disse tre værdier, når du opretter dit program **læste-d2c-meddelelser** .

![Azure portalen IoT Hub Messaging blade][6]

Du har nu oprettet din IoT hub, og du har IoT Hub hostname, IoT Hub forbindelsesstreng, IoT Hub primærnøgle, begivenhed Hub-kompatible navn og begivenhed Hub-kompatible slutpunkt, som du har brug for at fuldføre dette selvstudium.

## <a name="create-a-device-identity"></a>Oprette en enhed identitet

I dette afsnit, skal oprette du en Java console-app, der opretter en ny enhed identitet i identitet registreringsdatabasen i IoT-hub. En enhed kan ikke oprette forbindelse til IoT-hub, medmindre den har en indtastning i enhed identitet registreringsdatabasen. Se afsnittet **Enhed identitet registreringsdatabasen** af [IoT Hub Developer Guide] [ lnk-devguide-identity] kan finde flere oplysninger. Når du kører denne console-program, oprettes der et entydigt enheds-ID og -tasten, enheden kan bruge til at identificere sig, når der udsendes enhed i skyen meddelelser til IoT Hub.

1. Oprette en ny tom mappe med navnet iot-java-get-i gang. Oprette et projekt med Maven kaldet **oprette-enhed-identitet** ved hjælp af følgende kommando i din kommandoprompt i mappen iot-java-get-i gang. Bemærk, at dette er en enkelt, langt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå til den nye mappe i oprette-enhed – identitet din kommandoprompt.

3. Ved hjælp af en teksteditor, Åbn filen pom.xml i mappen Opret-enhed – identitet og tilføje følgende afhængigheden til noden **afhængigheder** . Dette gør det muligt at bruge pakken iothub-service-sdk i dit program:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Gem og Luk filen pom.xml.

5. Ved hjælp af en teksteditor, Åbn filen create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Tilføj følgende **importere** sætninger i filen:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Føje følgende klasse niveau variabler til klassen **App** , erstatte **{yourhubconnectionstring}** med værdien din relevante tidligere:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Ændre signaturen for den **primære** metode til at medtage undtagelserne på følgende måde:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Tilføj følgende kode som brødteksten i den **primære** metode. Denne kode opretter en enhed kaldet *javadevice* i IoT Hub identitet registreringsdatabasen, hvis ikke allerede findes. Derefter vises enheds-id og nøgle, du skal bruge senere:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Gem og Luk filen App.java.

11. For at opbygge programmet **Opret-enhed-identitet** ved hjælp af Maven skal du udføre følgende kommando kommandoprompten i mappen Opret-enhed-identitet:

    ```
    mvn clean package -DskipTests
    ```

12. Hvis du vil køre programmet **Opret-enhed-identitet** ved hjælp af Maven, skal du udføre følgende kommando kommandoprompten i mappen Opret-enhed-identitet:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Skal du notere af **enheds-id** og **nøglen enhed**. Du skal bruge disse senere, når du opretter et program, der opretter forbinder til IoT Hub som en enhed.

> [AZURE.NOTE] IoT Hub identitet registreringsdatabasen gemmer kun enhed identiteter for at aktivere sikker adgang til hubben. Det gemmer enheds-id'er og taster, der bruges som sikkerhedslegitimationsoplysninger og en aktiveret/deaktiveret flag, du kan bruge til at deaktivere adgang til en enkelt enhed. Hvis programmet skal gemme andre enhed-specifikke metadata, skal der bruges et program-specifikke store. Du [IoT Hub Developer Guide] [ lnk-devguide-identity] kan finde flere oplysninger.

## <a name="receive-device-to-cloud-messages"></a>Modtage enhed i skyen meddelelser

I dette afsnit, skal oprette du en Java console-app, som læser enhed i skyen meddelelser fra IoT Hub. En IoT hub Fremviser en [Begivenhed Hub][lnk-event-hubs-overview]-kompatible slutpunkt til gør det muligt at læse enhed i skyen meddelelser. Hvis du vil bevare ting enkel, opretter dette selvstudium en grundlæggende læser, ikke der passer til en høj overførselshastighed installation. [Behandle enhed i skyen meddelelser] [ lnk-process-d2c-tutorial] selvstudium viser, hvordan du behandle enhed i skyen meddelelser skaleres til brug. [Introduktion til begivenhed Hubs] [ lnk-eventhubs-tutorial] selvstudium indeholder yderligere oplysninger om, hvordan at behandle meddelelser fra begivenhed Hubs og er gældende for IoT Hub begivenhed Hub-kompatible slutpunkterne.

> [AZURE.NOTE] Begivenhed Hub-kompatible slutpunkt for at læse enhed i skyen meddelelser altid bruger AMQP-protokollen.

1. Oprette et projekt med Maven kaldet **læste-d2c-meddelelser** ved hjælp af følgende kommando i din kommandoprompt i den iot-java-get-gang mappe, du oprettede i afsnittet *oprette en enhed identitet* . Bemærk, at dette er en enkelt, langt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå til den nye mappe i læse-d2c-meddelelser fra din kommandoprompt.

3. Ved hjælp af en teksteditor, Åbn filen pom.xml i mappen læste-d2c-meddelelser og tilføje følgende afhængigheden til noden **afhængigheder** . Dette gør det muligt at bruge pakken eventhubs-klient i programmet til at læse fra begivenhed Hub-kompatible slutpunktet:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Gem og Luk filen pom.xml.

5. Ved hjælp af en teksteditor, Åbn filen read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Tilføj følgende **importere** sætninger i filen:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Tilføj følgende klasse niveau variabler til **App** -klasse. Erstatte **{youriothubkey}**, **{youreventhubcompatibleendpoint}**og **{youreventhubcompatiblename}** med de værdier, du nævnt tidligere:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Tilføj følgende **receiveMessages** metode til **App** -klasse. Denne metode opretter en **EventHubClient** forekomst for at oprette forbindelse til begivenhed Hub-kompatible slutpunkt og opretter derefter en forekomst af **PartitionReceiver** at læse fra en hændelse Hub partition asynkront. Det løkker løbende og udskriver oplysningerne, indtil programmet afsluttes.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Denne metode bruger et filter, når programmet opretter modtageren, så modtageren læser kun meddelelser, der sendes til IoT-Hub, når modtageren begynder at køre. Dette er nyttigt i et testmiljø, så du kan se det aktuelle sæt af meddelelser. I et produktionsmiljø din kode skal sørge for, at den behandler alle meddelelserne – se, [hvordan du behandle IoT Hub enhed i skyen meddelelser] [ lnk-process-d2c-tutorial] selvstudium kan finde flere oplysninger.

9. Ændre signaturen for den **primære** metode til at medtage undtagelsen på følgende måde:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Tilføj følgende kode til den **primære** metode i klassen **App** . Denne kode opretter to **EventHubClient** og **PartitionReceiver** forekomster og gør det muligt at lukke programmet, når du er færdig med at behandle meddelelser:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Koden antages, du har oprettet din IoT hub i det F1 (gratis) niveau. En gratis IoT hub har to partitioner med navnet "0" og "1".

11. Gem og Luk filen App.java.

12. For at opbygge programmet **læste-d2c-meddelelser** ved hjælp af Maven skal du udføre følgende kommando kommandoprompten i mappen læste-d2c-meddelelser:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal oprette du en Java console-app, der simulerer en enhed, som sender enhed i skyen meddelelser til en IoT-hub.

1. Oprette et projekt med Maven kaldet **simuleret enhed** ved hjælp af følgende kommando i din kommandoprompt iot-java-get-gang mappen i du oprettede i afsnittet *oprette en enhed identitet* . Bemærk, at dette er en enkelt, langt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå til den nye mappe simuleret enhed din kommandoprompt.

3. Ved hjælp af en teksteditor, Åbn filen pom.xml i mappen simuleret enhed og tilføje følgende afhængigheder til noden **afhængigheder** . Dette gør det muligt at bruge pakken iothub-java-klient i programmet til at kommunikere med dine IoT hub og sekventielt Java-objekter til JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Gem og Luk filen pom.xml.

5. Ved hjælp af en teksteditor, Åbn filen simulated-device\src\main\java\com\mycompany\app\App.java.

6. Tilføj følgende **importere** sætninger i filen:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Føje følgende klasse niveau variabler til klassen **App** , erstatte **{youriothubname}** med IoT hub navn og **{yourdevicekey}** med den enhed nøgleværdi, du har oprettet i afsnittet *oprette en enhed identitet* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Dette eksempelprogram bruger variablen **protokol** , når det starter et **DeviceClient** objekt. Du kan bruge enten HTTP eller AMQP protokol til at kommunikere med IoT Hub.

8. Tilføj følgende indlejrede **TelemetryDataPoint** klasse i klassen **App** til at angive den telemetridata enheden sender til din IoT hub:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Tilføj følgende indlejrede **EventCallback** klassen i klassen **App** til at vise bekræftelse status, der returnerer IoT hub, når det behandler en meddelelse fra simuleret enheden. Denne metode giver også besked tråd i programmet på computeren, når meddelelsen er blevet behandlet:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Tilføj følgende indlejrede **MessageSender** klassen inden for **App** -klassen. Metoden **køre** i klassen genererer telemetri eksempeldata til at sende til din IoT hub og venter på en bekræftelse, før du sender den næste meddelelse:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Denne metode sender en ny enhed i skyen meddelelse et sekund efter IoT hubben accepterer den forrige meddelelse. Meddelelsen indeholder et JSON-serialiseret objekt med deviceId og et tilfældigt tal til at simulere en vindhastighed bliver hastighed føler.

11. Erstat den **primære** metode med følgende kode, der opretter en tråd for at sende enhed i skyen meddelelser til dine IoT hub:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Gem og Luk filen App.java.

13. For at opbygge programmet **simuleret enhed** ved hjælp af Maven skal du udføre følgende kommando kommandoprompten i mappen simuleret enhed:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere retry-politikker (som en eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. Kør følgende kommando for at starte overvågning den første partition i din IoT hub ved en kommandoprompt i mappen læst d2c:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub service-klientprogrammet til at overvåge enhed i skyen meddelelser][7]

2. På en kommandoprompt i mappen simuleret enhed, du køre følgende kommando for at begynde at sende telemetridata til din IoT hub:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Java IoT Hub enhed klientprogrammet og sende meddelelser enhed i skyen][8]

3. Feltet **brugen** i [Azure portal] [ lnk-portal] viser antallet af meddelelser, der sendes til hubben:

    ![Azure portalen brugen felt viser antallet af meddelelser, der sendes til IoT-Hub][43]

## <a name="next-steps"></a>Næste trin

Konfigureret en ny IoT hub på portalen Azure i dette selvstudium, og derefter oprettet en enhed identitet i den hub identitet registreringsdatabasen. Du har brugt denne enhed identitet til at aktivere appen simuleret enhed til at sende enhed i skyen meddelelser til den pågældende hub. Du kan også oprettet en app, der viser de meddelelser, der modtages af hubben. 

Fortsætte med at komme i gang med IoT Hub og udforske se andre IoT scenarier:

- [Oprette forbindelse til din enhed][lnk-connect-device]
- [Kom godt i gang med administration af enheder][lnk-device-management]
- [Introduktion til IoT Gateway SDK][lnk-gateway-SDK]

Få vist [proces enhed i skyen meddelelser] for at lære at udvide din IoT løsning og behandle enhed i skyen meddelelser skaleres,[ lnk-process-d2c-tutorial] selvstudium.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/