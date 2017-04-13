<properties
    pageTitle="Sende meddelelser i skyen-til-enhed med IoT Hub | Microsoft Azure"
    description="Følge dette selvstudium for at lære at sende cloud-til-enhed meddelelser ved hjælp af Azure IoT Hub med C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Selvstudium: Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub og .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der hjælper med at aktivere pålidelig og sikker tovejskommunikation mellem millioner af IoT enheder og afslutte et program igen. [Komme i gang med IoT Hub] selvstudiet viser, hvordan du oprette en IoT-hub, klargøre en enhed identitet i den og kode en simuleret enhed, som sender enhed i skyen meddelelser.

Dette selvstudium bygger på [Kom i gang med IoT Hub]. Det kan du se hvordan til:

- Sende cloud-til-enhed meddelelser til en enkelt enhed via IoT Hub fra dit program skyen back-end.
- Modtage cloud-til-enhed meddelelser på en enhed.
- Fra dit program skyen slutningen, skal du anmode om levering bekræftelse (*feedback*) for meddelelser, der sendes til en enhed fra IoT Hub.

Du kan finde flere oplysninger i de meddelelser, cloud-til-enhed i [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

I slutningen af dette selvstudium, skal du køre to Windows console-programmer:

* **SimulatedDevice**, en ændret version af appen, der er oprettet i [komme i gang med IoT Hub], som opretter forbindelse til din IoT hub og modtager meddelelser i skyen-til-enhed.
* **SendCloudToDevice**, der sender meddelelsen cloud-til-enhed til den simulerede enhed via IoT-Hub, og derefter modtager dens levering bekræftelse.

> [AZURE.NOTE] IoT Hub har SDK understøttelse af mange Enhedsplatforme og sprog (herunder C, Java og Javascript) via Azure IoT enhed SDK'er. Du kan finde en trinvis vejledning i at tilslutte enheden til dette selvstudium kode og generelt til Azure IoT Hub [Azure IoT Developer Center].

For at fuldføre dette selvstudium, skal du bruge følgende:

+ Microsoft Visual Studio 2015

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

## <a name="receive-messages-on-the-simulated-device"></a>Modtage meddelelser på simuleret enheden

I dette afsnit, skal du ændre programmet simuleret enhed du har oprettet i [komme i gang med IoT Hub] for at modtage cloud-til-enhed meddelelser fra IoT-hubben.

1. Tilføj følgende metode til klassen **Program** i project **SimulatedDevice** i Visual Studio.

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    Den `ReceiveAsync` metode returnerer asynkront den modtagne meddelelse på det tidspunkt, hvor den modtages af enheden. Den returnerer *null* efter en specifiable timeout-periode (i dette tilfælde standard på et minut bruges). Når dette sker, bør koden fortsætte med at vente til nye meddelelser. Dette er årsagen til den `if (receivedMessage == null) continue` linje.

    Opkaldet til `CompleteAsync()` giver besked om IoT-Hub, meddelelsen er blevet behandlet korrekt. Meddelelsen kan fjernes fra enheden køen. Hvis noget er der sket, der ikke kan appen enhed fuldføre behandling af meddelelsen, leverer IoT Hub den igen. Det er derefter vigtigt, at meddelelsen behandlingslogik i appen enhed *idempotent*, så modtager den samme meddelelse flere gange giver det samme resultat. Et program kan også midlertidigt afbryde en meddelelse, der medfører IoT hub bevares meddelelsen i køen til fremtidige forbrug. Eller programmet kan afvise en meddelelse, som meddelelsen fjernes permanent fra køen. Du kan finde flere oplysninger om cloud-til-enhed meddelelse livscyklus du [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Når du bruger HTTP i stedet for MQTT eller AMQP som en transport, den `ReceiveAsync` metode returnerer med det samme. Understøttede mønsteret for cloud-til-enhed meddelelser med HTTP er periodisk forbundne enheder, der kontrollerer til meddelelser sjældent (mindre end hver 25 minutter). Udstede flere HTTP modtager resultater i IoT-Hub, throttling anmodningerne. Du kan finde flere oplysninger om forskellene mellem MQTT, AMQP og HTTP support og IoT Hub (throttling) se [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

2. Tilføj følgende metode i metoden **Main** lige før den `Console.ReadLine()` linje:

        ReceiveC2dAsync();

> [AZURE.NOTE] Dette selvstudium implementerer ikke nogen forsøg politik for enkels risvin. Fremstilling kode, skal du implementere forsøg politikker (som eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl].

## <a name="send-a-cloud-to-device-message"></a>Sende en meddelelse i skyen-til-enhed

I dette afsnit, skal du skrive en Windows-konsollen app, som sender meddelelser til skyen-til-enhed til appen simuleret enhed.

1. Oprette et nyt projekt i Visual C# skrivebord App ved hjælp af projektskabelon **Console-program** i den aktuelle løsning i Visual Studio. Navngive projektet **SendCloudToDevice**.

    ![Nyt projekt i Visual Studio][20]

2. I Solution Explorer, højreklik på løsningen, og klik derefter på **Administrer NuGet pakker til løsning...**. 

    Dette åbner vinduet **Administrer NuGet pakker** .

3. Søge efter `Microsoft Azure Devices`, klik på **Installer**, og Accepter vilkår for anvendelse. 

    Dette henter, installerer og tilføjer en reference til [Azure IoT - tjenesten SDK NuGet pakke].

4. Tilføj følgende `using` sætningen øverst i filen **Program.cs** :

        using Microsoft.Azure.Devices;

5. Tilføj følgende felter til klassen **Program** . Erstatte værdien pladsholder med IoT hub forbindelsesstrengen fra [komme i gang med IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Føje følgende metode til klassen **Program** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Denne metode sender en ny meddelelse i skyen-til-enhed til enheden med-ID'ET, `myFirstDevice`. Denne parameter skal ændres i overensstemmelse hermed, i tilfælde af, at du har ændret den fra den, der bruges i [komme i gang med IoT Hub].

7. Til sidst skal du føje følgende linjer til metoden **Main** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Højreklik på din løsning i Visual Studio, fra, og vælg **Angiv start projekter...**. Vælg **flere projekter, Start**og derefter vælge handlingen **Start** til **ProcessDeviceToCloudMessages**, **SimulatedDevice**og **SendCloudToDevice**.

9.  Tryk på **F5**. Alle tre programmer skal starte. Vælg vinduerne **SendCloudToDevice** , og tryk på **Enter**. Du bør se meddelelsen modtages af appen simuleret.

    ![App modtagelse meddelelse][21]

## <a name="receive-delivery-feedback"></a>Modtage leveringen feedback
Det er muligt at anmodning om levering (eller udløb) godkendelser fra IoT Hub til hver meddelelse i skyen-til-enhed. Dette gør det muligt for skyen back-end nemt underrette forsøg igen eller kompensation logik. Du kan finde flere oplysninger om cloud-til-enhed feedback, se [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

I dette afsnit, skal redigere du **SendCloudToDevice** app for at anmode om feedback og modtage fra IoT Hub.

1. Tilføj følgende metode til klassen **Program** i project **SendCloudToDevice** i Visual Studio.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Bemærk, at Modtag mønsteret er den samme, der bruges til at modtage cloud-til-enhed meddelelser fra app enhed.

2. Tilføj følgende metode i metoden **Main** lige efter den `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` linje:

        ReceiveFeedbackAsync();

3. For at få feedback for leveringen af meddelelsen cloud-til-enhed, skal du angive en egenskab i metoden **SendCloudToDeviceMessageAsync** . Tilføj følgende linje, lige efter den `var commandMessage = new Message(...);` linje:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Køre apps ved at trykke på **F5**. Du bør se alle tre programmer, der starter. Vælg vinduerne **SendCloudToDevice** , og tryk på **Enter**. Der skal vises en meddelelse, der modtages ved simuleret app og efter et par sekunder, feedback meddelelsen modtages af programmets **SendCloudToDevice** .

    ![App modtagelse meddelelse][22]

> [AZURE.NOTE] Dette selvstudium implementerer ikke nogen forsøg politik for enkels risvin. Fremstilling kode, skal du implementere forsøg politikker (som eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl].

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært hvordan du kan sende og modtage meddelelser i skyen-til-enhed. 

For at se eksempler på komplette til slut-løsninger, der bruger IoT-Hub skal du se [Azure IoT pakke].

Hvis du vil vide mere om udvikling af løsninger med IoT Hub skal du se [IoT Hub Developer Guide].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - tjenesten SDK NuGet pakke]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[IoT Hub Developer Guide]: iot-hub-devguide.md
[Komme i gang med IoT-Hub]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT pakke]: https://azure.microsoft.com/documentation/suites/iot-suite/