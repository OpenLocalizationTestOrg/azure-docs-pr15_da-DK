## <a name="send-messages-to-event-hubs"></a>Sende meddelelser til begivenhed hubber

I dette afsnit, skal du skrive en Windows-konsollen app, der sender begivenheder til begivenhed centrum.

1. Oprette et projekt med visuelle C# skrivebordet App ved hjælp af skabelonen **Console programmet** projekt i Visual Studio. Navngive projektet **afsender**.

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp1.png)

2. I Solution Explorer, højreklik på løsningen, og klik derefter på **Administrer NuGet pakker til løsning**. 

3. Klik på fanen **Gennemse** , og derefter søge efter `Microsoft Azure Service Bus`. Sørg for, at navnet på projektet (**afsender**) er angivet i feltet **versioner** . Klik på **Installer**, og Accepter vilkår for anvendelse. 

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp2.png)

    Visual Studio henter, installerer og tilføjer en reference til [Azure Service Bus bibliotek NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Tilføj følgende `using` sætninger øverst i filen **Program.cs** :

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Tilføj følgende felter til klassen **Program** , erstatter pladsholder værdier med navnet på den hændelse-Hub, du oprettede i forrige afsnit og navneområde niveau forbindelsesstrengen du gemte tidligere.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Føje følgende metode til klassen **Program** :

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Denne metode sender løbende begivenheder til din begivenhed hubben med en 200 ms forsinkelse.

7. Til sidst skal du føje følgende linjer til metoden **Main** :

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```
