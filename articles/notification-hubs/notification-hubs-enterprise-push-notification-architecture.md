<properties
    pageTitle="Meddelelse om Hubs - Enterprise Push-arkitektur"
    description="Vejledning til brug af Azure meddelelse Hubs i et virksomhedsmiljø"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Enterprise opslagsnål arkitektonisk vejledning

Virksomheder er i dag gradvist glidende for at skabe mobilprogrammer til enten deres slutbrugere (ekstern) eller for medarbejderne (internt). De har eksisterende back end-systemer i stedet være det mainframes eller nogle LoB-programmer, der skal integreres i mobile programmet arkitekturen. Denne vejledning, der taler om, hvordan bedst at gøre denne integration anbefale mulig løsning til almindelige scenarier.

En hyppige kravet skal bruges til at sende opslagsnål meddelelse brugere via deres mobile program, når en hændelse af interesse indtræffer i back end-systemer. F.eks. en bankkunde med bankens bank app på hendes iPhone ønsker at få besked, når et kredit er foretaget over en bestemt afstand fra hendes konto eller et intranet scenarie, hvor en medarbejder fra økonomiafdelingen, der har en budget godkendelse app på sin Windows Phone ønsker at få besked, når hun får en anmodning om godkendelse.

Bank- eller godkendelse behandling sandsynligvis skal udføres i nogle back end-system, der skal starte en opslagsnål til brugeren. Der kan være flere sådanne back end-systemer, der skal alle oprette den samme type logik til at implementere opslagsnål, når en hændelse udløser en meddelelse. Kompleksitet her ligger i integrering af flere back end-systemer sammen med et enkelt push-system, hvor slutbrugerne kan har abonneret på forskellige beskeder, og der kan også være flere mobilprogrammer fx tale om intranet mobilapps hvor ét mobile program måske vil modtage beskeder fra flere sådanne back end-systemer. Back end-systemer ikke kender eller har brug for at vide af opslagsnål semantik/teknologi, så en almindelig løsning traditionelt har været at introducere en komponent som undersøger back end-systemer til en hvilken som helst begivenheder interesse og er ansvarlig for at sende push-meddelelser til klienten.
Her kan vi taler om en endnu bedre ved hjælp af Azure Service Bus - emne/abonnement model, der begrænser kompleksitet, mens du foretager løsningen SVG-løsning.

Her er den generelle arkitektur løsningens (generalized med flere mobilapps men lige gældende, når der er kun én mobilapp)

## <a name="architecture"></a>Arkitektur

![][1]

Det vigtigste stykke i dette diagram over arkitektur er Azure Service Bus, som indeholder en emner/abonnementer programming model (mere om den på [Service Bus Pub/Sub programming]). Den modtager, som det er tilfældet, er den Mobile backend-version (typisk [Azure Mobile Service], som starter en opslagsnål til apps til mobil) modtager ikke mails direkte fra back end-systemer, men i stedet har vi en mellemliggende fremstilling lag, der leveres af [Azure Service Bus] , som gør det muligt for mobile back-end at modtage meddelelser fra en eller flere back end-systemer. Et Service Bus emne skal oprettes for hver af back end-systemerne fx-konto, HR, økonomi, som er stort set "emner" af interesse, som starter meddelelser sendes som push meddelelse. Back end-systemer sender meddelelser til disse emner. En Mobile back-end kan abonnere på et eller flere af disse emner ved at oprette et Service Bus abonnement. Det berettiger mobile back-end at modtage en meddelelse fra den tilsvarende backend-system. Mobile back-end fortsætter med at lytte til meddelelser på deres abonnementer, og når der modtages en meddelelse, den omdannes tilbage og sender den som en meddelelse til dens meddelelse-hub. Meddelelse om hubs leverer derefter til sidst meddelelsen mobilappen. Hvis du vil opsummere de vigtigste komponenter, har vi så:

1. Back end-systemer (LoB/ældre systemer)
    - Opretter Service Bus emne
    - Sender meddelelse
2. Mobile backend-version
    - Opretter abonnement til tjenesten
    - Modtager meddelelse (fra back end-system)
    - Sender besked til klienter (via Azure meddelelse Hub)
3. Mobile-program
    - Modtager og Vis meddelelse

###<a name="benefits"></a>Fordele:

1. Afkobling mellem modtager (mobile app/service via meddelelse Hub) og afsender (back end-systemer) gør det muligt for flere back end-systemer integreres med minimale ændring.
2. Dette gør scenarie med flere mobilapps at modtage begivenheder fra en eller flere back end-systemer.  

## <a name="sample"></a>Eksempel:

###<a name="prerequisites"></a>Forudsætninger
Du skal udføre følgende selvstudier til at lære med begreber samt oprettelse af almindelige & trinnene til konfiguration af:

1. [Tjenesten Bus Pub/Sub programming] - det forklarer detaljerne for at arbejde med tjenesten Bus emner/abonnementer, hvordan du opretter et navneområde indeholde emner/abonnementer, hvordan du kan sende og modtage meddelelser fra dem.
2. [Meddelelse om Hubs - Windows Universal selvstudium] - det forklarer, hvordan du kan konfigurere en Windows Store app og bruge meddelelse Hubs til at registrere og derefter modtage beskeder.

###<a name="sample-code"></a>Eksempel på kode

Komplet eksempelkoden er aktiveret i [Meddelelse Hub eksempler]. Det er opdelt i tre komponenter:

1. **EnterprisePushBackendSystem**

    en. Dette projekt bruger *WindowsAzure.ServiceBus* Nuget pakke og er baseret på [tjenesten Bus Pub/Sub programmering].

    b. Dette er en simpel C# console-app til at simulere en LobSystem, starter meddelelsen blev leveret til mobilappen.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`bruges til at oprette emnet Service Bus, hvor vi sender meddelelser.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`bruges til at sende meddelelser til denne tjeneste Bus emne. Her sender vi blot et sæt af tilfældige meddelelser til emnet med jævne mellemrum med henblik på eksemplet. Normalt vil der være et backend-system der så vil sende meddelelser, når en hændelse indtræffer.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    en. Dette projekt bruger *WindowsAzure.ServiceBus* og *Microsoft.Web.WebJobs.Publish* Nuget-pakker og er baseret på [tjenesten Bus Pub/Sub programmering].

    b. Dette er en anden C# console app som vi vil køre som en [Azure WebJob] , da den skal køre løbende for at lytte til meddelelser fra LoB/back end-systemer. Dette er en del af din Mobile backend-version.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`bruges til at oprette et Service Bus abonnement til emnet, hvor back end-systemet sender meddelelser. Afhængigt af scenariet business opretter denne komponent en eller flere abonnementer til tilsvarende emner (fx nogle modtager muligvis meddelelser fra HR-system, nogle fra økonomi system og osv.)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification bruges til at læse meddelelsen fra emnet ved hjælp af dens abonnement, og hvis læse lykkes derefter Udform en meddelelse (i dette eksempel scenario en oprindelig Windows toastbeskeden) for at blive sendt til den mobile program ved hjælp af Azure meddelelse Hubs.

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. Højreklik på løsningen i Visual Studio til publicering dette som en **WebJob**, og vælg **Udgiv som WebJob**

    ![][2]

    f. Vælg din publicering profil og oprette et nyt Azure-websted, hvis den ikke findes allerede som skal være vært for denne WebJob, og når du har websted og derefter **Publicer**.

    ![][3]

    g. Konfigurere jobbet for at være "Køre løbende", så når du logger på [Azure klassisk Portal] skal du se ud som følger:

    ![][4]


3. **EnterprisePushMobileApp**

    en. Dette er et Windows Store-program, som skal modtage toast beskeder fra WebJob kører som en del af din Mobile backend-version og få den vist. Dette er baseret på [Meddelelse Hubs - Windows Universal selvstudium].  

    b. Sørg for, at dit program er aktiveret til at modtage toast meddelelser.

    c. Sikre, at følgende meddelelse hubberne registreringskode hedder på Appen starter (når du har udskiftet *HubName* og *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Kører eksempel:

1. Sikre, at din WebJob kører korrekt og planlagt til at "Køre løbende".
2. Køre **EnterprisePushMobileApp** , begynder Windows Store-app.
3. Kør programmet **EnterprisePushBackendSystem** console som vil simulere LoB back-end og begynder at sende meddelelser og skal du se afsnittet toast meddelelser vises som følger:

    ![][5]

4. Meddelelserne blev oprindeligt blev sendt til Service Bus emner, som blev overvåges ved Service Bus abonnementer i Web tingene. Når blev modtaget en meddelelse, er en meddelelse om oprettet og sendt til mobilappen. Du kan gennemse loggene WebJob for at bekræfte behandling, når du går til linket logfiler i [Azure klassisk Portal] for Web tingene:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Meddelelse om Hub eksempler]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Tjenesten Bus Pub/Sub programmering]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Meddelelse om Hubs - Windows Universal selvstudium]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure klassisk Portal]: https://manage.windowsazure.com/
