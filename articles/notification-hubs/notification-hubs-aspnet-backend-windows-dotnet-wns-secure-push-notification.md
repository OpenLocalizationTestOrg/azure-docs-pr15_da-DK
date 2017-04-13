<properties
    pageTitle="Azure meddelelse Hubs sikker Push"
    description="Lær at sende sikker pushmeddelelser i Azure. Kodeeksempler, der er skrevet i C#-brug af .NET-API."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Azure meddelelse Hubs sikker Push

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Oversigt

Understøttelse af push meddelelse i Microsoft Azure gør muligt at få adgang til en nemt Hvis du vil bruge, flere platforme, skaleret push-infrastruktur, hvilket gør det meget forenkler implementeringen af pushmeddelelser for både forbruger- og enterprise-programmer til mobile platforme.

På grund af lovmæssige eller sikkerhedsbegrænsninger, nogle gange et program kan du medtage noget i meddelelsen, som ikke kan overføres via standard opslagsnål meddelelse infrastruktur. Dette selvstudium beskrives, hvordan du opnå den samme oplevelse ved at sende følsomme oplysninger via en sikker, godkendt forbindelse mellem klientenhed og app back end.

På et højt niveau som strømmen på følgende måde:

1. App-back-end:
    - Gemmer sikker data i back end-databasen.
    - Sender ID'ET for denne meddelelse på enheden (der sendes ingen sikker oplysninger).
2. Appen på enheden, når du modtager meddelelsen:
    - Enheden kontakter den back end-anmoder om sikker data.
    - Appen kan vise data som en meddelelse på enheden.

Det er vigtigt at være opmærksom på, i det foregående flow (og i dette selvstudium), antager vi, at enheden gemmer et godkendelse token i lokale lager, når brugeren logger på. Dette garanterer en helt problemfri oplevelse, som enheden kan hente på beskeden sikker data ved hjælp af dette symbol. Hvis dit program ikke gemme godkendelsestokens på enheden, eller hvis disse tokens kan være udløbet, appen enhed ved modtagelse af meddelelsen skal vise en generisk meddelelse spørge brugeren til at starte appen. Appen derefter godkender brugeren og viser besked om overførslen.

Selvstudiet Secure opslagsnål viser, hvordan du sende en meddelelse om opslagsnål sikkert. Selvstudiet bygger på selvstudium [Giv brugerne besked](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , så du skal fuldføre trinnene i dette selvstudium først.

> [AZURE.NOTE] Dette selvstudium antages det, at du har oprettet og konfigureret hub din meddelelse, som beskrevet i [Komme i gang med besked om Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Bemærk også, Windows Phone 8.1 kræver Windows (ikke Windows Phone)-legitimationsoplysninger, og opgaver i baggrunden ikke virker på Windows Phone 8.0 eller Silverlight 8.1. For Windows Store-programmer, du kan modtage meddelelser via en baggrund opgave, kun, hvis appen er låseskærm aktiveret (Klik på afkrydsningsfeltet i Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Redigere Windows Phone-projekt

1. Føj følgende kode til App.xaml.cs til at registrere opslagsnål baggrund opgave i projektet **NotifyUserWindowsPhone** . Tilføj følgende linje af kode i slutningen af den `OnLaunched()` metode:

        RegisterBackgroundTask();

2. Stadig i App.xaml.cs, skal du tilføje følgende kode umiddelbart efter den `OnLaunched()` metode:

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Tilføj følgende `using` sætninger øverst i filen App.xaml.cs:

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. Klik på **Gem alt**i menuen **filer** i Visual Studio.

## <a name="create-the-push-background-component"></a>Oprette komponenten opslagsnål baggrund

Næste trin er at oprette komponenten opslagsnål baggrund.

1. I Solution Explorer skal du højreklikke på den øverste knude løsningens (**løsning SecurePush** i dette tilfælde), klik på **Tilføj**og derefter skal du klikke på **Nyt projekt**.

2. Udvid **Store Apps**, og derefter klikke på **Windows Phone Apps**, og klik derefter **Windows Runtime-komponent (Windows Phone)**. Navngiv projektet **PushBackgroundComponent**, og klik derefter på **OK** for at oprette projektet.

    ![][12]

3. I Solution Explorer, skal du højreklikke på projektet **PushBackgroundComponent (Windows Phone 8.1)** og derefter klikke på **Tilføj**, og klik derefter **klasse**. Navngiv den nye klasse **PushBackgroundTask.cs**. Klik på **Tilføj** for at generere klassen.

4. Erstatte hele indholdet af **PushBackgroundComponent** navneområde definitionen med følgende kode, erstatte pladsholderen `{back-end endpoint}` til back end-slutpunktet, der fås under installation af din back end-:

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. I Solution Explorer, skal du højreklikke på projektets **PushBackgroundComponent (Windows Phone 8.1)** , og klik derefter på **Administrer NuGet pakker**.

6. I venstre side skal du klikke på **Online**.

7. Skriv **Http Client**i feltet **Søg** .

8. Klik på **Microsoft HTTP-klientbiblioteker**på resultatlisten, og klik derefter på **Installer**. Fuldføre installationen.

9. Skriv **Json.net**tilbage i NuGet **Søg** i. Installer **Json.NET** pakken, og derefter lukke vinduet NuGet pakke Manager.

10. Tilføj følgende `using` sætninger øverst i filen **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. Højreklik på **referencer**i Solution Explorer i project **NotifyUserWindowsPhone (Windows Phone 8.1)** og derefter klikke på **Tilføj Reference**. Markér afkrydsningsfeltet ud for **PushBackgroundComponent**i dialogboksen Reference Manager, og klik derefter på **OK**.

12. I Solution Explorer, skal du dobbeltklikke på **Package.appxmanifest** i projektet **NotifyUserWindowsPhone (Windows Phone 8.1)** . Under **meddelelser om**at angive **Toast kan** til **Ja**.

    ![][3]

13. Klik på menuen **erklæringer** i øverst stadig i **Package.appxmanifest**. Klik på **Opgaver i baggrunden**i rullemenuen **Tilgængelige erklæringer** , og klik derefter på **Tilføj**.

14. Markér **Push meddelelse**i **Package.appxmanifest**under **Egenskaber**.

15. Skriv **PushBackgroundComponent.PushBackgroundTask** i feltet **Indgangspunkt** i **Package.appxmanifest**, under **Indstillinger**.

    ![][13]

16. Klik på **Gem alt**i menuen **filer** .

## <a name="run-the-application"></a>Køre programmet

Hvis du vil køre programmet, skal du gøre følgende:

1. I Visual Studio, skal du køre programmet **AppBackend** Web API. Der vises en ASP.NET-webside.

2. I Visual Studio, skal du køre **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone app. Windows Phone emulatoren kører og indlæser appen automatisk.

3. Angive et brugernavn og din adgangskode i appen **NotifyUserWindowsPhone** brugergrænseflade. Det kan være en streng, men de skal være den samme værdi.

4. Klik på i appen **NotifyUserWindowsPhone** brugergrænseflade, **logge på og registrere**. Klik derefter på **Send opslagsnål**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
