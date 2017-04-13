<properties
    pageTitle="Sende meddelelser på tværs af platforme til brugere med besked om Hubs (ASP.NET)"
    description="Lær at bruge meddelelse Hubs skabeloner til at sende i en enkelt anmodning, en platforme meddelelser, der er beregnet til alle platforme."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Sende meddelelser på tværs af platforme til brugere med besked om Hubs


I det forrige selvstudium [Informer brugerne med besked om Hubs], du har lært Sådan push-beskeder til alle enheder, der er registreret af en bestemt godkendte bruger. I dette selvstudium flere anmodninger der kræves for at sende en meddelelse til hver understøttede klient-platform. Meddelelse om Hubs understøtter skabeloner, som gør det muligt at angive, hvordan en bestemt enhed ønsker at modtage beskeder. Dette forenkler afsendelse af meddelelser på tværs af platforme. Dette emne beskrives, hvordan du kan udnytte skabeloner til at sende i en enkelt anmodning, en platforme meddelelser, der er beregnet til alle platforme. Kan finde mere detaljerede oplysninger om skabeloner under [Azure meddelelse Hubs oversigt][Templates].

> [AZURE.NOTE] Meddelelse om Hubs giver en enhed til at registrere flere skabeloner med det samme mærke. I dette tilfælde resulterer en indgående meddelelse målretning af mærket i flere meddelelser, der er leveret til enheden, én for hver skabelon. Dette gør det muligt at få vist den samme meddelelse i flere visuelle meddelelser, som begge som en badge og som en toast-besked i en Windows Store app.

Benytte følgende fremgangsmåde for at sende meddelelser på tværs af platforme om brug af skabeloner:

1. Udvid mappen **enheder** i Solution Explorer i Visual Studio, og derefter åbne filen RegisterController.cs.

2. Find blokering af kode i metoden **Post** , som opretter en ny registrering Erstat den `switch` indhold med følgende kode:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Denne kode kalder metoden platform-specifikke for at oprette en skabelon registrering i stedet for en oprindelig registrering. Eksisterende registreringer skal ikke ændres, fordi skabelon registreringer, der er afledt af oprindelige registreringer.

3. Erstat metoden **sendNotification** i controller **meddelelser** med følgende kode:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Denne kode sender en meddelelse til alle platforme, på samme tid og uden at angive en oprindelig data. Besked om Hubs opbygger og leverer de korrekte data til alle enheder med værdien for det angivne _mærke_ , som angivet i de registrerede skabeloner.

4. Publicere dit WebApi back end-projekt.

5. Køre appen klienten igen, og Bekræft, at registrering af lykkes.

6. (Valgfrit) Installere appen klient til en anden enhed og derefter køre appen.

    Bemærk, at en meddelelse vises på hver enhed.

## <a name="next-steps"></a>Næste trin

Nu hvor du har fuldført dette selvstudium, kan du få mere at vide om meddelelse Hubs og skabeloner i disse emner:

+ **[Brug meddelelse Hubs til at sende seneste nyheder]** <br/>Viser et andet scenarie for brug af skabeloner

+  **[Azure meddelelse Hubs oversigt][Templates]**<br/>Oversigt over emne har mere detaljerede oplysninger om skabeloner.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Brug meddelelse Hubs til at sende seneste nyheder]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Give brugerne besked med besked om Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
