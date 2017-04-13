<properties
    pageTitle="Sende meddelelser i skyen-til-enhed med IoT Hub | Microsoft Azure"
    description="Følge dette selvstudium for at lære at sende cloud-til-enhed meddelelser ved hjælp af Azure IoT Hub med Java."
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
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Selvstudium: Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub og Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der hjælper med at aktivere pålidelig og sikker tovejskommunikation mellem millioner af IoT enheder og afslutte et program igen. [Komme i gang med IoT Hub] selvstudiet viser, hvordan du oprette en IoT-hub, klargøre en enhed identitet i den og kode en simuleret enhed, som sender enhed i skyen meddelelser.

Dette selvstudium bygger på [Kom i gang med IoT Hub]. Det kan du se hvordan til:

- Sende cloud-til-enhed meddelelser til en enkelt enhed via IoT Hub fra dit program skyen back-end.
- Modtage cloud-til-enhed meddelelser på en enhed.
- Fra dit program skyen slutningen, skal du anmode om levering bekræftelse (*feedback*) for meddelelser, der sendes til en enhed fra IoT Hub.

Du kan finde flere oplysninger i de meddelelser, cloud-til-enhed i [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

I slutningen af dette selvstudium skal køre du to Java console-programmer:

* **simuleret enhed**, en ændret version af appen, der er oprettet i [komme i gang med IoT Hub], som opretter forbindelse til din IoT hub og modtager meddelelser i skyen-til-enhed.
* **send-c2d-meddelelser**, der sender meddelelsen cloud-til-enhed til den simulerede enhed via IoT-Hub, og derefter modtager dens levering bekræftelse.

> [AZURE.NOTE] IoT Hub har SDK understøttelse af mange Enhedsplatforme og sprog (herunder C, Java og Javascript) via Azure IoT enhed SDK'er. Du kan finde en trinvis vejledning i at tilslutte enheden til dette selvstudium kode og generelt til Azure IoT Hub [Azure IoT Developer Center].

For at fuldføre dette selvstudium skal bruge du følgende:

+ Java SE 8. <br/> [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Java til dette selvstudium på Windows eller Linux.

+ Maven 3.  <br/> [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Maven til dette selvstudium på Windows eller Linux.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

## <a name="receive-messages-on-the-simulated-device"></a>Modtage meddelelser på simuleret enheden

I dette afsnit, skal du ændre programmet simuleret enhed du har oprettet i [komme i gang med IoT Hub] for at modtage cloud-til-enhed meddelelser fra IoT-hubben.

1. Ved hjælp af en teksteditor, Åbn filen simulated-device\src\main\java\com\mycompany\app\App.java.

2. Tilføje klassen følgende **MessageCallback** som en indlejret klasse inden for **App** -klassen. **Udfør** metode startes, når enheden modtager en meddelelse fra IoT Hub. I dette eksempel enheden altid giver besked om hubben, at den er fuldført meddelelsen:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Ændre den **primære** metode til at oprette en forekomst af **MessageCallback** og kalde metoden **setMessageCallback** , før det åbner klienten på følgende måde:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Hvis du bruger HTTP i stedet for MQTT eller AMQP som transport, kontrollerer **DeviceClient** forekomst for meddelelser fra IoT Hub sjældent (mindre end hver 25 minutter). Du kan finde flere oplysninger om forskellene mellem MQTT, AMQP og HTTP support og IoT Hub (throttling) se [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Sende en meddelelse i skyen-til-enhed

I dette afsnit, skal oprette du en Java console-app, som sender meddelelser til skyen-til-enhed til appen simuleret enhed. Du skal bruge enheden-id'et for den enhed, du har tilføjet i [komme i gang med IoT Hub] selvstudiet. Du kan også bruge forbindelsesstrengen til din IoT-hub, som du kan finde i [Azure-portalen].

1. Oprette et Maven projekt, der hedder **send-c2d-meddelelser** ved hjælp af følgende kommando i din kommandoprompt. Bemærk, at dette er en enkelt, langt kommando:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Gå til den nye send-c2d-meddelelser mappe din kommandoprompt.

3. Ved hjælp af en teksteditor, Åbn filen pom.xml i mappen send-c2d-meddelelser og tilføje følgende afhængigheden til noden **afhængigheder** . Tilføje afhængigheden gør det muligt at bruge pakken **iothub-java-service-klient** i programmet til at kommunikere med din IoT hub tjeneste:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Gem og Luk filen pom.xml.

5. Ved hjælp af en teksteditor, Åbn filen send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Tilføj følgende **importere** sætninger i filen:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Føje følgende klasse niveau variabler til klassen **App** , erstatte **{yourhubconnectionstring}** og **{yourdeviceid}** med værdierne din relevante tidligere:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Erstat den **primære** metode med følgende kode, der opretter forbindelse til din IoT-hub, sender en meddelelse til din enhed og derefter venter på en bekræftelse, at enheden modtaget og behandlet meddelelsen:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Dette selvstudium implementerer ikke nogen forsøg politik for enkels risvin. Fremstilling kode, skal du implementere forsøg politikker (som eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl].

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. På en kommandoprompt i mappen simuleret enhed, du køre følgende kommando for at begynde at sende telemetri til IoT-hub og lytte til skyen-til-enhed meddelelser, der sendes fra dit hub:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Køre appen simuleret enhed][img-simulated-device]

2. På en kommandoprompt i mappen send-c2d-meddelelser, du køre følgende kommando for at sende en meddelelse i skyen-til-enhed, og vent på en feedback bekræftelse:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Kør kommandoen til at sende meddelelsen cloud-til-enhed][img-send-command]

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært hvordan du kan sende og modtage meddelelser i skyen-til-enhed. 

For at se eksempler på komplette til slut-løsninger, der bruger IoT-Hub skal du se [Azure IoT pakke].

Hvis du vil vide mere om udvikling af løsninger med IoT Hub skal du se [IoT Hub Developer Guide].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Komme i gang med IoT-Hub]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub Developer Guide]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portalen]: https://portal.azure.com
[Azure IoT pakke]: https://azure.microsoft.com/documentation/suites/iot-suite/