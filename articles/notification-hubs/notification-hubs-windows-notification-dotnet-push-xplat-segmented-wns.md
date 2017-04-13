<properties
    pageTitle="Brug meddelelse Hubs til at sende seneste nyheder (Windows Universal)"
    description="Brug Azure meddelelse Hubs med koder i registreringen til at sende seneste nyheder til en universal Windows-app."
    services="notification-hubs"
    documentationCenter="windows"
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

# <a name="use-notification-hubs-to-send-breaking-news"></a>Brug meddelelse Hubs til at sende seneste nyheder


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du bruger Azure meddelelse Hubs til at udsende seneste nyheder meddelelser til en Windows Store eller Windows Phone 8.1 (ikke-Silverlight) app. Hvis du målretter Windows Phone 8.1 Silverlight, kan du se den [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) version. Når du er færdig, skal du kunne tilmelde Bryd nyhedskategorier, du er interesseret i, og modtage kun pushmeddelelser for disse kategorier. Dette scenario er et almindeligt mønster for mange apps, hvor meddelelser skal sendes til de grupper af brugere, der har tidligere er erklæret interesse i dem, f.eks. RSS-læseren, apps til musikelskere, og så videre. 

Broadcast scenarier er aktiveret ved at medtage en eller flere _mærker_ , når du opretter en registrering i hubben meddelelse. Når meddelelser er sendt til et mærke, får alle enheder, der har registreret til mærket meddelelsen. Fordi mærker er blot strenge, har de ikke klargøres på forhånd. Se [meddelelse Hubs Routing og mærke udtryk](notification-hubs-tags-segment-push-message.md)kan finde flere oplysninger om mærker.

##<a name="prerequisites"></a>Forudsætninger

Dette emne, der er baseret på den app, du oprettede i [komme i gang med besked om Hubs][get-started]. Før du starter selvstudiet, du skal allerede har udført [komme i gang med besked om Hubs][get-started].

##<a name="add-category-selection-to-the-app"></a>Tilføje valg af kategori på appen

Det første trin er at tilføje elementer på Brugergrænsefladen til din eksisterende primære side, der gør det muligt at vælge kategorier til at registrere. De kategorier, der er valgt som en bruger er gemt på enheden. Når programmet starter, oprettes en enhed registrering i din meddelelse hubben med de valgte kategorier som mærker.

1. Åbn filen MainPage.xaml project, og derefter kopiere følgende kode i elementet **gitter** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Højreklik på **delt** projektet og tilføje en ny klasse med navnet **beskeder**, føje **offentlige** ændring til klassedefinitionen og derefter føje følgende **ved hjælp af** udtalelser til den nye kodefil:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Kopier den følgende kode i den nye klasse **meddelelser** :

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Denne klasse anvender det lokale lager til lagring af de forskellige kategorier af nyheder, er at modtage denne enhed. Bemærk, at i stedet for kalder metoden *RegisterNativeAsync* vi kalder *RegisterTemplateAsync* til at registrere for kategorierne ved hjælp af en skabelon registrering. 
    
    Vi giver også et navn til skabelonen ("simpleWNSTemplateExample"), fordi vi måske til at registrere mere end én skabelon (for eksempel en til toast meddelelser) og en felter og vi brug for at give dem et navn for at kunne opdatere eller slette dem.

    Bemærk, at hvis en enhed registrerer flere skabeloner med det samme mærke, en indgående meddelelse målretning, mærke, der medfører flere meddelelser leveres til enheden (en for hver skabelon). Denne funktionalitet er nyttig, når den samme logiske meddelelse har medføre flere visuelle meddelelser, for eksempel viser både en badge og en hilsen i et Windows Store-program.

    Du kan finde flere oplysninger om skabeloner, [skabeloner](notification-hubs-templates-cross-platform-push-messages.md).




4. Tilføj følgende egenskab til **App** -klasse i projektfil App.xaml.cs:

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Denne egenskab bruges til at oprette og få adgang til en forekomst af **meddelelser** .

    I den ovenstående kode, skal du erstatte den `<hub name>` og `<connection string with listen access>` pladsholdere med din anmeldelse hub navn og forbindelsesstrengen for *DefaultListenSharedAccessSignature* , som du tidligere har hentet.

    > [AZURE.NOTE] Da legitimationsoplysninger, som er delt med en klient app ikke generelt sikker, skal du kun fordele tasten for at lytte få adgang til din klient-app. Lyt adgang gør det muligt for din app til at registrere for beskeder, men eksisterende registreringer ikke kan ændres, og meddelelser kan blive sendt. Tasten fuld adgang bruges i en sikret back end-tjeneste til at sende meddelelser og ændre eksisterende registreringer.

5. Tilføj følgende linje i din MainPage.xaml.cs:

        using Windows.UI.Popups;

6. Tilføj følgende metode i projektfil MainPage.xaml.cs:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Denne metode opretter en liste over kategorier og bruger klassen **beskeder** til at gemme listen i det lokale lager og registrere de tilsvarende mærker med din anmeldelse hub. Når kategorier ændres, genskabes registreringen med de nye kategorier.

Din app er nu i stand til at gemme et sæt kategorier i lokale lager på enheden og registrere med hubben besked, når brugeren ændrer markeringen af kategorier.

##<a name="register-for-notifications"></a>Tilmelding til meddelelser

Disse trin Registrer meddelelse hub på starttilstand ved hjælp af de kategorier, der er gemt i lokale lager.

> [AZURE.NOTE] Da kanalen URI tildelt ved Windows besked om Service (WNS) kan ændres til enhver tid, skal du registrere til meddelelser ofte for at undgå besked om fejl. I dette eksempel registrerer til besked om hver gang appen startes. Til apps, der udføres ofte, kan mere end en gang om dagen, du sandsynligvis springe registrering Hvis du vil bevare båndbredde mindre end en dag er gået siden forrige registrering.

1. Åbn filen App.xaml.cs og opdatere **InitNotificationsAsync** metoden til at bruge den `notifications` klasse abonnere baseret på kategorier.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    Det sikrer, at hver gang appen startes den henter kategorierne fra lokale lager og anmoder om en registeration for disse kategorier. Metoden **InitNotificationsAsync** blev oprettet som en del af den [komme i gang med besked om Hubs] [ get-started] selvstudium.

3. Tilføj følgende kode til metoden *OnNavigatedTo* i projektfil MainPage.xaml.cs:

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
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

    ![][19]

4. Sende en besked om ny fra backend-version i en af følgende måder:

    + **Console app:** starter console-app.

    + **Java/PHP:** køre din app/script.

    Beskeder for de valgte kategorier vises som toast meddelelser.

    ![][14]

##<a name="next-steps"></a>Næste trin

I dette selvstudium lært vi at transmittere seneste nyheder efter kategori. Overvej fuldfører en af følgende selvstudier, der fremhæver andre avancerede meddelelse Hubs scenarier:

+ [Brug meddelelse Hubs transmittere oversatte seneste nyheder]

    Lær at udvide den seneste nyheder app for at aktivere afsendelse oversatte meddelelser.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Brug meddelelse Hubs transmittere oversatte seneste nyheder]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
