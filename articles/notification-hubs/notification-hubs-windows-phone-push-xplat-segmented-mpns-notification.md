<properties
    pageTitle="Brug meddelelse Hubs til at sende seneste nyheder (Windows Phone)"
    description="Brug Azure meddelelse Hubs til at bruge mærke registreringer til at sende seneste nyheder til en Windows Phone app."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Brug meddelelse Hubs til at sende seneste nyheder

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du bruger Azure meddelelse Hubs til at udsende seneste nyheder meddelelser til en Windows Phone 8.0/8.1 Silverlight-app. Hvis du målretter Windows Store- eller Windows Phone 8.1 app, skal du se i til [Windows Universal](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) versionen. Når du er færdig, skal du kunne tilmelde Bryd nyhedskategorier, du er interesseret i, og modtage kun pushmeddelelser for disse kategorier. Dette scenario er et almindeligt mønster for mange apps, hvor meddelelser skal sendes til de grupper af brugere, der har tidligere er erklæret interesse i dem, fx RSS-læseren, apps til musikelskere osv.

Broadcast scenarier er aktiveret ved at medtage en eller flere _mærker_ , når du opretter en registrering i hubben meddelelse. Når meddelelser er sendt til et mærke, får alle enheder, der har registreret til mærket meddelelsen. Fordi mærker er blot strenge, har de ikke klargøres på forhånd. Se [meddelelse Hubs Routing og mærke udtryk](notification-hubs-tags-segment-push-message.md)kan finde flere oplysninger om mærker.

##<a name="prerequisites"></a>Forudsætninger

Dette emne, der er baseret på den app, du oprettede i [komme i gang med besked om Hubs]. Før du starter selvstudiet, skal du allerede har udført [komme i gang med besked om Hubs].

##<a name="add-category-selection-to-the-app"></a>Tilføje valg af kategori på appen

Det første trin er at tilføje elementer på Brugergrænsefladen til din eksisterende primære side, der gør det muligt at vælge kategorier til at registrere. De kategorier, der er valgt som en bruger er gemt på enheden. Når programmet starter, oprettes en enhed registrering i din meddelelse hubben med de valgte kategorier som mærker.

1. Åbn filen MainPage.xaml projekt og derefter erstatte de **gitter** elementer med navnet `TitlePanel` og `ContentPanel` med følgende kode:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Oprette en ny klasse med navnet **beskeder**, føje **offentlige** ændring til klassedefinitionen i projektet, og derefter føje følgende **ved hjælp af** udtalelser til den nye kodefil:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Kopier den følgende kode i den nye klasse **meddelelser** :

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Denne klasse anvender det isolerede lager til lagring af de forskellige kategorier af nyheder, denne enhed, der skal modtage. Den indeholder også metoder til at tilmelde dig disse kategorier ved hjælp af en [skabelon](notification-hubs-templates-cross-platform-push-messages.md) meddelelse registrering.


4. Tilføj følgende egenskab i projektfil App.xaml.cs til **App** -klasse. Erstatte den `<hub name>` og `<connection string with listen access>` pladsholdere med din anmeldelse hub navn og forbindelsesstrengen for *DefaultListenSharedAccessSignature* , som du tidligere har hentet.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Da legitimationsoplysninger, som er delt med en klient app ikke generelt sikker, skal du kun fordele tasten for at lytte få adgang til din klient-app. Lyt adgang gør det muligt for din app til at registrere for beskeder, men eksisterende registreringer ikke kan ændres, og meddelelser kan blive sendt. Tasten fuld adgang bruges i en sikret back end-tjeneste til at sende meddelelser og ændre eksisterende registreringer.

5. Tilføj følgende linje i din MainPage.xaml.cs:

        using Windows.UI.Popups;

6. Tilføj følgende metode i projektfil MainPage.xaml.cs:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Denne metode opretter en liste over kategorier og bruger klassen **beskeder** til at gemme listen i det lokale lager og registrere de tilsvarende mærker med din anmeldelse hub. Når kategorier ændres, genskabes registreringen med de nye kategorier.

Din app er nu i stand til at gemme et sæt kategorier i lokale lager på enheden og registrere med hubben besked, når brugeren ændrer markeringen af kategorier.

##<a name="register-for-notifications"></a>Tilmelding til meddelelser

Disse trin Registrer meddelelse hub på starttilstand ved hjælp af de kategorier, der er gemt i lokale lager.

> [AZURE.NOTE] Da kanalen URI tildelt ved den Microsoft Push meddelelse Service (MPNS) kan ændres til enhver tid, skal du registrere til meddelelser ofte for at undgå besked om fejl. I dette eksempel registrerer til meddelelser, hver gang appen startes. Til apps, der udføres ofte, kan mere end en gang om dagen, du sandsynligvis springe registrering Hvis du vil bevare båndbredde mindre end en dag er gået siden forrige registrering.


1. Åbn filen App.xaml.cs og tilføje **asynkron** ændring i **Application_Launching** metode og erstatte den meddelelse Hubs registreringskode, du tilføjede i [komme i gang med besked om Hubs] med følgende kode:

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    Det sikrer, at hver gang appen startes den henter kategorierne fra lokale lager og anmoder om en registrering for disse kategorier.

2. Tilføj følgende kode, der implementerer metoden **OnNavigatedTo** i projektfil MainPage.xaml.cs:

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }

    Dette opdaterer hovedsiden baseret på status for tidligere gemt kategorier.

Appen er nu færdig og kan gemme et sæt kategorier i det lokale enhed-lager, der bruges til at registrere med hubben besked, når brugeren ændrer markeringen af kategorier. Vi vil derefter definere en back-end, der kan sende kategori beskeder til denne app.

##<a name="sending-tagged-notifications"></a>Sende mærket beskeder

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Køre appen og generere beskeder

1. Tryk på F5 for at samle og start-appen i Visual Studio.

    ![][1]

    Bemærk, at app Brugergrænsefladen indeholder et sæt slår, hvor du kan vælge kategorier for at abonnere på.

2. Aktivere en eller flere kategorier skifter, og derefter klikke på **Abonner**.

    Appen konverterer de valgte kategorier til mærker og anmoder om en anden enhed registrering for de valgte mærker fra meddelelse-hubben. De registrerede kategorier returneres og vises i en dialogboks.

    ![][2]

3. Når du modtager en bekræftelse, at dine kategorier var abonnement, der er fuldført, køre console-app for at sende beskeder for hver kategorier. Kontrollér, at du kun modtager en meddelelse for de kategorier, du har tilmeldt dig.

    ![][3]

Du har fuldført dette emne.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Komme i gang med besked om Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

