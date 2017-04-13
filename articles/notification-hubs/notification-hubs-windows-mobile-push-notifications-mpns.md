<properties
    pageTitle="Sender pushmeddelelser med Azure meddelelse Hubs på Windows Phone | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs til pushmeddelelser til et Windows Phone 8 eller Windows Phone 8.1 Silverlight-program."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="Push-meddelelse, push meddelelse opslagsnål på windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Sender pushmeddelelser med Azure meddelelse Hubs på Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

Dette selvstudium viser, hvordan du bruger Azure meddelelse Hubs til at sende pushmeddelelser til en Windows Phone 8 eller Windows Phone 8.1 Silverlight-program. Hvis du målretter Windows Phone 8.1 (ikke-Silverlight), derefter henvise til [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) versionen.
I dette selvstudium, kan du oprette en tom Windows Phone 8-app, der modtager pushmeddelelser ved hjælp af Microsoft opslagsnål meddelelse Service (MPNS). Når du er færdig, skal du kunne bruge din meddelelse hub transmittere pushmeddelelser til alle de enheder, der kører din app.

> [AZURE.NOTE] Meddelelse om Hubs Windows Phone SDK understøtter ikke ved hjælp af den Windows Push meddelelse Service (WNS) til Windows Phone 8.1 Silverlight apps. Hvis du vil bruge WNS (i stedet for MPNS) til Windows Phone 8.1 Silverlight apps, skal du følge [Meddelelse Hubs - Windows Phone Silverlight selvstudium], der bruger REST API'er.

Selvstudiet demonstrerer dette simple broadcast scenario på ved hjælp af besked om Hubs.

##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ [Visual Studio 2012 Express til Windows Phone], eller en nyere version.

Fuldfør dette selvstudium er en betingelse for alle andre besked om Hubs selvstudier til Windows Phone 8-apps.

##<a name="create-your-notification-hub"></a>Oprette din meddelelse hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Klik på sektionen <b>Meddelelse Services</b> (i <i>Indstillinger</i>), klik på <b>Windows Phone (MPNS)</b> , og klik derefter på afkrydsningsfeltet <b>Aktiver ikke-godkendte opslagsnål</b> .</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal - Aktivér unathenticated pushmeddelelser](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Din hub er nu oprettet og konfigureret til at sende ikke-godkendte meddelelser til Windows Phone.

> [AZURE.NOTE] Dette selvstudium bruger MPNS i ikke-godkendte tilstand. MPNS ikke-godkendte tilstand leveres med begrænsninger på meddelelser, som du kan sende til hver kanal. Meddelelse om Hubs understøtter [MPNS godkendte tilstand](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) ved at gøre det muligt at overføre dit certifikat.

##<a name="connecting-your-app-to-the-notification-hub"></a>Oprette forbindelse din app til hubben meddelelse

1. Oprette et nyt Windows Phone 8-program i Visual Studio.

    ![Visual Studio - projekt - Windows Phone App][13]

    I Visual Studio 2013 opdatering 2 eller nyere kan oprette du i stedet en Windows Phone Silverlight-program.

    ![Visual Studio - projekt - tom App – Windows Phone Silverlight][11]

2. Højreklik på løsningen i Visual Studio, og klik derefter på **Administrer NuGet pakker**.

    Dette viser dialogboksen **Administrer NuGet pakker** .

3. Søge efter `WindowsAzure.Messaging.Managed` og klikke på **Installer**, og derefter Accepter vilkår for anvendelse.

    ![Visual Studio - NuGet Package Manager][20]

    Dette henter, installerer og tilføjer en reference til biblioteket Azure Messaging til Windows ved hjælp af <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet pakke</a>.

4. Åbn filen App.xaml.cs og tilføje følgende `using` sætninger:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Føj følgende kode øverst i **Application_Launching** metode i App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] Værdien **MyPushChannel** er et indeks, der bruges til at slå en eksisterende kanal i samlingen [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Hvis der ikke er en der skal du oprette en ny post med dette navn.
    
    Sørg for at indsætte navnet på din hub og forbindelsesstrengen kaldet **DefaultListenSharedAccessSignature** , som du hentede i forrige afsnit.
    Denne kode henter kanalen URI for appen fra MPNS og registrerer derefter denne kanal URI med din anmeldelse hub. Det garanterer, der også kanalen URI er registreret i din meddelelse hub hver gang programmet startes.

    >[AZURE.NOTE]Dette selvstudium sender en toast-besked til enheden. Når du sender en meddelelse om felt, skal du i stedet kalde metoden **BindToShellTile** på kanalen. Understøtter begge toast og flisen meddelelser skal du ringe både **BindToShellTile** og **BindToShellToast**.

6. I Solution Explorer skal du udvide **Egenskaber**, åbne den `WMAppManifest.xml` filer, klik på fanen **Funktioner** , og Sørg for, at muligheden for **ID_CAP_PUSH_NOTIFICATION** er markeret.

    ![Visual Studio - Windows Phone App-funktioner][14]

    Dette sikrer, at din app kan modtage pushmeddelelser. Uden at det mislykkes ethvert forsøg på at sende en meddelelse om opslagsnål til appen.

7. Tryk på den `F5` for at køre appen.

    Meddelelsen registrering vises i appen.

8. Lukke appen.  

   >[AZURE.NOTE] Hvis du vil modtage en opslagsnål toastbeskeden, være programmet ikke kører i forgrunden.

##<a name="send-push-notifications-from-your-backend"></a>Sende pushmeddelelser fra din backend-version

Du kan sende pushmeddelelser ved hjælp af besked om Hubs fra en hvilken som helst backend-version via den offentlige <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">RESTEN interface</a>. I dette selvstudium sender du pushmeddelelser ved hjælp af en .NET console-program. 

Du kan finde et eksempel på, hvordan du sender pushmeddelelser fra en ASP.NET WebAPI back-end, der er integreret med besked om Hubs [Azure meddelelse Hubs Giv brugerne med .NET back end](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Et eksempel på, hvordan du vil sende pushmeddelelser ved hjælp af [REST API'er](https://msdn.microsoft.com/library/azure/dn223264.aspx), skal du se [Sådan bruger du besked om Hubs fra Java](./notification-hubs-java-push-notification-tutorial.md) og [hvordan du bruger meddelelse Hubs fra PHP](./notification-hubs-php-push-notification-tutorial.md).

1. Højreklik på løsningen, Vælg **Tilføj** og **Nyt projekt...**, og derefter under **Visual C#**, skal du klikke på **Windows** og **Console programmet**og klikke på **OK**.

    ![Visual Studio - projekt - Console-programmer][6]

    Dette tilføjer en ny Visual C# console-program til løsningen. Du kan også gøre dette i en separat løsning.

4. Klik på **Funktioner**, skal du klikke på **Bibliotek Package Manager**, og klik **Pakke Manager-konsollen**.

    Dette viser pakke Manager-konsollen.

5.  Angiv den **standard project** til din nye console programmet projekt i vinduet **Pakke Manager-konsollen** , og derefter i vinduet console skal du udføre følgende kommando:

        Install-Package Microsoft.Azure.NotificationHubs

    Dette indsætter en reference til Azure meddelelse Hubs SDK ved hjælp af <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet pakke</a>.

6. Åbn den `Program.cs` filen, og Tilføj følgende `using` sætning:

        using Microsoft.Azure.NotificationHubs;

6. I den `Program` klasse, skal du tilføje følgende metode:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Sørg for at erstatte den `<hub name>` pladsholder med navnet på den meddelelse-hub, der vises i portalen. Erstat også pladsholderen forbindelse streng med forbindelsesstrengen kaldes **DefaultFullSharedAccessSignature** , som du hentede i afsnittet "Konfigurer din meddelelse hub."

    >[AZURE.NOTE]Sørg for, at du bruger forbindelsesstrengen med **fuld** adgang, ikke **lytte** adgang. Strengen lytte access har ikke tilladelse til at sende pushmeddelelser.

4. Tilføj følgende linje i din `Main` metode:

         SendNotificationAsync();
         Console.ReadLine();

5. Med din Windows Phone-emulator kører og din app, der er lukket, skal du angive console programmet projektet som standard startprojektet, og tryk derefter på den `F5` for at køre appen.

    Du modtager en opslagsnål toastbeskeden. Trykke på banneret toast indlæser appen.

Du kan finde alle mulige overførsler i [toast-katalog] og [flisen katalog] -emner på MSDN.

##<a name="next-steps"></a>Næste trin

I denne enkle eksempel udsendes du pushmeddelelser til alle dine Windows Phone 8-enheder. 

Referere til Selvstudium i [Brug meddelelse Hubs til pushmeddelelser til brugere] for at målrette bestemte brugere. 

Hvis du vil inddele dine brugere ved interesse grupper, kan du læse [Brug meddelelse Hubs sende seneste nyheder]. 

Få mere at vide mere om, hvordan du bruger meddelelse Hubs i [Meddelelse Hubs vejledning].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express til Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Meddelelse om Hubs vejledning]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Bruge meddelelse Hubs til pushmeddelelser til brugere]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Brug meddelelse Hubs til at sende seneste nyheder]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast-katalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[feltet katalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Meddelelse om Hubs - Windows Phone Silverlight selvstudium]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

